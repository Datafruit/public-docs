# hadoop接入

> ## 概要　　
> * 本流程主要演示的是把 `hadoop` 上的 `csv` 数据导入到Druid。  
> * `csv` 数据样式： `1500711222228,165,cye1z8` （第一个字段的类型为 `date` ,格式为`millis`）

> 前提：  
> 1.部署好druid平台环境  
> 2.部署好hadoop环境

## 第一步：导入数据到hdfs(如果数据已经在hdfs上则不用)
在部署了 hadoop 环境的机器上进行操作。
1. 切换到 hdfs 用户，指令为:  
`su hdfs`
2. 在 hdfs 上创建存放数据的目录，指令为:  
`hadoop fs -mkdir -p dirName`  
例如，要把数据放到 hdfs 下的 /data1/tmp/druid 下，指令为:  
`hadoop fs -mkdir -p /data1/tmp/druid`
3. 将数据放到目录下，指令为：  
`hadoop fs -put fileName dirName`  
例如，将 /data/tmp/test.json 文件放到hdfs下的 /data1/tmp/druid 下，指令为:  
`hadoop fs -put /data/tmp/test.json /data1/tmp/druid`

## 第二步：创建json文件
创建、编辑、保存 taskspec.json 文件说明：
1. 按照数据的格式，创建 taskspec.json 文件，具体参数参照 [`task-spec.json`](#json)。  
2. 执行指令：  
`cd /data1/tmp/druid`  
进入该目录下，创建 json 文件。指令为  
`vim taskspec.json`  
将 json 内容拷贝，保存退出。

**说明**：taskspec.json 文件定义 csv 文件分2种，一种是有时间列，一种是没时间列，需要将json文件中的
```
"timestampSpec":{
  "column":"ts",
  "format":"millis"
}
```  
改成：  
```
"timestampSpec":{
  "missingValue":"2017-08-01T12:00:000Z"
}
```  
(固定时间，时间可改)。其中ts为表中时间列的列名。

## 第三步：执行命令,建立task
1. 进入存放 taskspec.json 文件的目录：  
`cd /data1/tmp/druid`  
2. 执行建立task的指令：  
```shell
curl -X 'POST' -H 'Content-Type:application/json' -d @task-spec.json http://{overlordIP}:8090/druid/indexer/v1/task
```
- **`task-spec.json：`** json文件的名字  
- **`overlordIp`**： druid的overlord节点ip地址


## 第四步：查看Task执行情况
1. 查看日志  
访问：`http://{OverlordIP}:8090/console.html`，点击 `Task` 的日志，查看 `Task` 的执行情况

   > **OverlordIP:** druid的overlord节点ip地址
   
   ![](/assets/LuceneIndexTaskPics/log.jpg)

2. 查看执行结果  
使用 `sugo-plyql` 查询 `Task` 的执行结果，具体的命令格式为：
```shell
./plyql -h {OverlordIP} -q 'select count(*) from {datasource}' 
```
   > **OverlordIP:** druid的overlord节点ip地址  
   > **datasource:** `json` 配置文件中定义的 `datasource` 名称
   
   如果查询的结果是"No Such Datasorce"，则说明数据接入没有成功。  
   如果数据接入成功，那么查询到的结果如下：  
   ![](/assets/LuceneIndexTaskPics/result_plyql.jpg)  
   该数字为数据条数。
   
   关于 `sugo-plyql` 的安装和使用，详见[ sugo-plyql 使用文档](/developer/interfaces/sugo-plyql.md)

## 第五步：停止task（需要时再用）
在需要停止 task 时，可以发送如下 http post 请求停止 task 任务  
```shell
curl -X 'POST' -H 'Content-Type:application/json' http://{overlordIP}:8090/druid/indexer/v1/task/{taskId}/shutdown
```  
- **`overlord_ip`**： druid的overlord节点ip地址
- **`taskId`**： 在 `http://{overlordIP}:8090/console.html` task详细页面对应 id 列的信息

## <a id="json" href="json"></a> task-spec.json详细配置如下：
```json
{
    "type": "lucene_index_hadoop",
    "spec": {
        "dataSchema": {
            "dataSource": "com_HyoaKhQMl_project_r1U1FDLjg",
            "parser": {
                "type": "string",
                "parseSpec": {
                    "format": "csv",
                    "timestampSpec": {
                        "column": "da",
                        "format": "millis"
                    },
                    "listDelimiter": ",",
                    "columns": [
                        "da",
                        "id",
                        "md5_str"
                    ],
                    "dimensionsSpec": {
                        "dynamicDimension": false,
                        "dimensions": [
                            {"name": "id","type": "int"},
                            {"name": "md5_str","type": "string"}
                        ]
                    }
                }
            },
            "metricsSpec": [],
            "granularitySpec": {
                "type": "uniform",
                "segmentGranularity": "MONTH",
                "queryGranularity": "NONE",
                "intervals": ["2016-05-21/2017-07-02"]
            }
        },
        "ioConfig": {
            "type": "hadoop",
            "inputSpec": {
                "type": "static",
                "paths": "/data1/tmp/test"
            }
        },
        "tuningConfig": {
            "type": "hadoop",
            "partitionsSpec": {
                "numShards": 3
            },
            "jobProperties": {
               "mapreduce.job.queuename": "root.default",
               "mapreduce.job.classloader": "true",
               "mapreduce.job.classloader.system.classes": "-javax.validation.,java.,javax.,org.apache.commons.logging.,org.apache.log4j.,org.apache.hadoop.,com.hadoop.compression"
           }
        }
    }
}
```

- **`spec.dataSchema.parser.parseSpec.timestampSpec.column:`** 时间戳列

- **`spec.dataSchema.parser.parseSpec.timestampSpec.format:时间格式类型:`** 推荐`millis`  

  - **`yy-MM-dd HH:mm:ss.SSS:`** 自定义的时间格式
  - **`auto:`** 自动识别时间，支持`iso`和`millis`格式
  - **`iso:`** iso标准时间格式，如`2016-08-03T12:53:51.999Z`
  - **`posix:`** 从1970年1月1日开始所经过的秒数,10位的数字
  - **`millis:`** 从1970年1月1日开始所经过的毫秒数，13位数字

- **`spec.dataSchema.parser.parseSpec.listDelimiter:`** csv列分隔符  

- **`spec.dataSchema.parser.parseSpec.columns:`** 维度列表，包含时间戳列，如：`["ts","ProductID"]`  

- **`spec.dataSchema.parser.parseSpec.dimensionsSpec.dimensions:`** 维度定义列表，不包含时间戳列，每个维度的格式为：```{“name”: “name_string”, “type”:”type_string”}```。Type支持的类型：`string`、`int`、`float`、`long`、`date`

- **`spec.dataSchema.granularitySpec.intervals:`** 数据时间戳范围,不能为空

- **`spec.dataSchema.granularitySpec.segmentGranularity:`** 段粒度，根据每天的数据量进行设置。  
小数据量建议DAY，大数据量（每天百亿）可以选择`HOUR`。可选项：`SECOND`、`MINUTE`、`FIVE_MINUTE`、`TEN_MINUTE`、`FIFTEEN_MINUTE`、`HOUR`、`SIX_HOUR`、`DAY`、`MONTH`、`YEAR`。

- **`spec.dataSchema.dataSource:`** 数据源的名称，类似关系数据库中的表名

- **`spec.ioConfig.type:`** 这里是从`hadoop`接入数据，固定为`hadoop`  
- **`spec.ioConfig.inputSpec.paths:`** 数据文件在hdfs上的目录,注意该目录下是否有多个文件


- **`spec.tuningConfig.partitionsSpec.numShards:`** 指定分片数
- **`spec.tuningConfig.jobProperties.mapreduce.job.queuename:`** 指定yarn上的队列名
- **`spec.tuningConfig.jobProperties.mapreduce.job.classloader:`** 如果为true，`task`会用 `druid` 中的 `hadoop-dependencies` ，而不是 `hadoop` 集群的配置

### json文件中需要特别注意的是以下几个参数：
1. **`spec.dataSchema.parser.parseSpec.columns:`** 包含时间戳列，并且先后顺序要和源数据对应
2. **`spec.dataSchema.parser.parseSpec.dimensionsSpec.dimensions:`** 不包含时间戳列，并且`name`要和`parser.parseSpec.columns`对应，`type`要和源数据对应起来。
3. **`spec.ioConfig.inputSpec.paths:`** 数据文件在hdfs上的目录，注意是否配置正确
4. 数据源有 date 类型时，要对 taskspec.json 文件 date 类型按要求定义：
    > Date类型格式 2017-01-01 00:00，则定义为："date","format":"yy-MM-dd HH:mm"  
    > Date类型格式 2017-01-01 00:00:00，则定义为："date","format":"yy-MM-dd HH:mm:ss"  
    > Date类型格式 2017-01-01，则定义为："date","format":"yy-MM-dd"  
    > Date类型格式 2017-01，则定义为："date","format":"yyyy-MM"  
    > Date类型格式 2017，则定义为："date","format":"yyyy"  
    > Date类型格式是从1970年1月1日开始所经过的秒数，10位的数字，则定义为："date","format":"posix"  
    > Date类型格式是从1970年1月1日开始所经过的毫秒数，13位数字，则定义为："date","format":"millis"  
5. 如果 `segmentGranularity` 即段粒度较小， `interval` 的范围也要较小，否则分片过多，非常耗时。  