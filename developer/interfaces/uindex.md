> ## 概要　　
> 本文档主要说明如何导入数据到`UIndex`以及从`UIndex`导出数据。

# 一、创建数据源
创建数据源需要往集群中的master机器上发送JSON，发送方式为`POST`，请求URL地址为：
http://masterHost:8086/druid/hmaster/v1/datasources

- 其中'masterHost'为master机器的IP或主机名。

`JSON`示例如下：
```
{
    "datasourceName": "user_profile",
    "partitions": 10,
    "columnDelimiter": "|",
    "shardSpec": {
        "type": "single",
        "dimension": "uid"
    },
    "dimensions": [
        {
          "name": "uid",
          "type": "string"
        },
        {
          "name": "user_name",
          "type": "string"
        },
        {
          "name": "age",
          "type": "int"
        },
	    {
          "name": "favorite",
          "type": "string",
          "hasMultipleValues":true
        },
        {
          "name": "income",
          "type": "double"
        }
    ]
}
```
`JSON`说明：
```
1）datasourceName：数据源名称；
2）partitions：分区数；
3）columnDelimiter：列分隔符；
4）shardSpec：分区方式（目前只支持按主键值hash分区）
  4.1）dimension：用来分区的主键名；
5）dimensions：维度信息
  5.1）name：维度名；
  5.2）type：维度类型，目前支持的类型有（string、int、long、float、double、date）；
  5.3）hasMultipleValues：该维度是否为多值，默认为false（单值）。
```

数据源创建成功后，会分别向数据库的`druid_hyperdatasource`表和`druid_hypersegments`表中插入元数据记录。

1）druid_hyperdatasource：保存数据源信息（数据源名称、创建时间、JSON信息），每个数据源对应一条记录；

2）druid_hypersegments：保存数据源对应的段的信息。如果该数据源的分区数为N，则会产生N个段，该表中会有对应的N条记录。

# 二、查看段的分布
当master创建数据源成功后，会将段分配给集群中的region server，region server收到master分配的任务后，会加载各自负责的段。可以通过下面的接口查询段在region server中的分布情况：

## 2.1、查看所有数据源段的分布

http://masterHost:8086/druid/hmaster/v1/datasources/serverview

- 其中'masterHost'为master机器的IP或主机名。

返回`JSON`示例
```
{
    "user_profile": {
        "0": [
            "192.168.0.216:8087"
        ],
        "1": [
            "192.168.0.217:8087"
        ]
    },
    "user_profile_tag": {
        "0": [
            "192.168.0.216:8087"
        ],
        "1": [
            "192.168.0.217:8087"
        ],
        "2": [
            "192.168.0.216:8087"
        ],
        "3": [
            "192.168.0.217:8087"
        ]
    }
}
```
上面的JSON说明：

1）数据源"user_profile"有两个分区，分区为0的段在机器216上，分区为1的段在机器217上；

2）数据源"user_profile_tag"有四个分区，0、2分区的段在机器216上，1、3分区的段在机器217上。

## 2.2、查看某个数据源writable段的分布

http://masterHost:8086/druid/hmaster/v1/datasources/serverview/writable/dataSourceName

- 其中'dataSourceName'为数据源名称。

返回`JSON`示例
```
{
    "0": [
        "192.168.0.216:8087"
    ],
    "1": [
        "192.168.0.217:8087"
    ]
}
```
上面的JSON说明：分区为0的可写段在机器216上，分区为1的可写段在机器217上。

> 【注】如果通过该接口成功查询到段在region server中的分布，说明region server已经加载完各自负责的段，已准备好接收写请求。此时就可以向集群中写数据了。

## 2.3、查看某个数据源readonly段的分布

http://masterHost:8086/druid/hmaster/v1/datasources/serverview/readonly/dataSourceName

- 其中'dataSourceName'为数据源名称。返回的JSON格式和writable的相同。

# 三、写入数据
UIndex提供了一个client端的jar包，可以通过该包方便的往集群中写入（新增、更新、删除）数据。该client端的git地址为：
https://github.com/Datafruit/sugo-druid-hyper-client

client端支持API调用方式和MapReduce方式写入数据。

## 3.1、API调用方式
### 1、新增
调用方式如下：
```java
import com.google.common.collect.Lists;
import com.google.common.collect.Maps;
import io.druid.hyper.client.imports.DataSender;

import java.util.List;
import java.util.Map;

DataSender sender = null;
try {
    sender = DataSender.builder().toServer("192.168.0.66:8086").ofDataSource("user_profile").build();
    sender.add("1001|Tom|18|swim,sing|18.0");  // 通过指定分隔符添加
    sender.add(Lists.newArrayList("1002", "Jack", "26", "basketball,football", "16.8"));
    sender.add(Lists.newArrayList("1003", "July", "24", "dance", "20.6")); // 通过List添加
} catch(Exception e) {
    ......
} finally {
    if (sender != null) {
        sender.close();
    }
}
```
> 【注】新增数据的时候，只需要传数据的值即可，但是值的顺序和个数要严格和创建数据源时"dimensions"指定的维度顺序和数量对齐。
> 比如：上面创建"user_profile"数据源时，指定的5个维度分别为：uid、user_name、age、favorite、income，则新增数据时也要按顺序提供5个对应的值。

### 2、更新
调用方式如下：
```java
DataSender sender = null;
try {
    sender = DataSender.builder().toServer("192.168.0.66:8086").ofDataSource("user_profile").build();

    // 通过Map的方式更新
    Map dataMap = ImmutableMap.of("uid", "1001", "income", "30"); // 更新income列
    sender.update(dataMap);

    // 通过List指定列和指定值的方式更新
    List columns = Lists.newArrayList("uid", "favorite", "income"); // 更新favorite和income列
    sender.update(columns, Lists.newArrayList("1002", "baseball", "50.2"));
    sender.update(columns, Lists.newArrayList("1003", "piano,skiing", "26"));
} catch(Exception e) {
    ......
} finally {
    if (sender != null) {
        sender.close();
    }
}
```
> 【注】更新数据的时候，可以更新指定的维度，但是每一次更新都必须要指定主键。在上面的示例中，无论何种方式的更新，主键"uid"必须要指定。

### 3、删除
调用方式如下：
```java
DataSender sender = null;
try {
    sender = DataSender.builder().toServer("192.168.0.66:8086").ofDataSource("user_profile").build();
    sender.delete(Lists.newArrayList("1001", "1002", "1003"));
} catch(Exception e) {
    ......
} finally {
    if (sender != null) {
        sender.close();
    }
}
```
> 【注】删除数据的时候，只需要指定待删除行的主键值即可。

## 3.2、MapReduce调用方式
MapReduce方式写数据的入口类为：`io.druid.hyper.client.imports.mr.ImportJob` ，调用方式为：

```shell
hadoop jar druid-hyper-client-1.0.0.jar -jobname -input -output -datasource -hmaster -action -columns
```

参数说明如下：
```
1）jobname：任务名称；
2）input：任务的输入路径；
3）output：任务的输出路径；
4）datasource：要写入的数据源名称；
5）hmaster：集群中master服务器的地址，格式 "host:port" 或者 "ip:port"；
6）action：写入的动作。A：新增；U：更新；D：删除；
7）columns：要更新的列名，多个列以逗号","隔开（column1,column2,...），新增或删除操作该参数不用指定。
```

# 四、导出数据
client端的包也提供了数据导出的API，可以将数据导出到本地或者HDFS，数据支持文件写入和流写入两种方式。
## 4.1、导出数据到本地文件
示例代码如下：
```java
ScanQuery query = ScanQuery.builder()
    .select("uid,user_name")  // 指定列名，多个列以逗号","隔开
    .from("user_profile")     // 指定数据源名称
    .limit(10000)             // 指定导出的行数
    .build();

DataExporter.local()
    .fromServer("192.168.0.216:8086")      // 指定master机器的地址
    .toFile("D:\\data1\user_profile.csv")  // 指定数据输出的本地文件
    .inCSVFormat()                         // 指定数据输出的格式（CSV、TSV、Hive或者自己指定列分隔符）
    .withQuery(query)
    .export();
```

## 4.2、导出数据到HDFS文件
示例代码如下：
```java
ScanQuery query = ScanQuery.builder()
    .select("uid,user_name")
    .from("user_profile")
    .limit(10000)
    .build();

DataExporter.hdfs()
    .fromServer("192.168.0.216:8086")
    .toFile("/hadoop/data1/user_profile")  // 指定数据输出的远程HDFS文件
    .inHiveFormat()                        // 指定数据输出的格式为Hive默认格式
    .withQuery(query)
    .export();
```

## 4.3、导出数据到本地流
示例代码如下：
```java
ScanQuery query = ...;
DataExporter.local()
    .fromServer("192.168.0.216:8086")
    .toStream(outputStream)      // 指定本地流
    .inFormat("|")               // 指定列分隔符
    .withQuery(query)
    .export();
```

## 4.4、导出数据到远程流
示例代码如下：
```java
ScanQuery query = ...;
DataExporter.hdfs()
    .fromServer("192.168.0.216:8086")
    .toStream(outputStream)      // 指定远程流
    .inFormat(";")               // 指定列分隔符
    .withQuery(query)
    .export();
```