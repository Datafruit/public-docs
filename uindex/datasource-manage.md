# 数据源管理接口
1.获取数据源列表接口  
2.删除数据源接口(不删数据信息,相当于停用)  
3.删除数据源接口(删除数据源相关的数据)  
4.创建数据源接口  
4.1创建数据源的例子:  
5.获取数据源列表接口(包含segment详细信息)  
6.获取数据源列表接口(只包含segment数量)  
7.获取指定数据源信息  
8.查询所有数据源所有`segment`所在的`regionServer`  
9.查询指定数据源下所有`segment`所在的`regionServer`  
10.查询指定数据源下所有`segment`的信息  
11.查询指定数据源下所有`segment`只读副本所在的`regionServer`  
12.查询指定数据源下所有`segment`可写副本所在的`regionServer`  

## 1.获取数据源列表接口
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources`  

返回结果:  
```
[
    "datasource1",
    "datasource2"
]
```

## 2.删除数据源接口(不删数据信息,相当于停用)
`curl -X DELETE http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/{datasourceName}`  
返状态码:200  
返回信息:空  

## 3.删除数据源接口(删除数据源相关的数据)
`curl -X DELETE http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/force/{datasourceName}`  
返状态码:200  
返回信息:`delete {dataSourceName} successfully`  

## 4.创建数据源接口
创建数据源的接口为:  
```
curl -X POST 'http://{hmaster_ip}:8086/druid/hmaster/v1/datasources' -H 'Content-Type:application/json' -d '{data}'  
```
`{hmaster_ip}`需要指定主`hmaster`的地址  
`{data}`:描述`datasource`的信息,主要包括:  
`datasourceName`:数据源名称  
`partitions`:分片数量,如果数据量大或者后期的数据增长比较快,建议将分片数设置大一些.分片将按一定的策略分配到多个`hregionServer`.`hregionServer`负责管理各个分片的数据.  
`shardSpec`:指定主键列,用于数据的分片.一般只需要设置`dimension`,即主键列名,比如用户id.  
`columnDelimiter`:数据写入时,数据列之间的分隔符.  
`dimensions`:维度信息  
`dimensions.type`:维度类型,目前支持的类型包括`int`,`long`,`float`,`double`,'string'.日期类型建议使用long类型表示,日期值转成13位的时间戳,  
`dimensions.name`:维度名称  
`dimensions.hasMultipleValues`:该维度是否是多值列,默认为`false`  

### 4.1创建数据源的例子
```
curl -X POST 'http://{hmaster_ip}:8086/druid/hmaster/v1/datasources' -H 'Content-Type:application/json' -d '
{
    "datasourceName": "datasource1",
    "partitions": 4,
    "shardSpec": {
        "type": "single",
        "dimension": "event_id",
        "partitionNum": -1
    },
    "columnDelimiter": "\u0001",
    "dimensions": [
    {
        "type": "string",
        "name": "event_id",
        "hasMultipleValues": false
    },
    {
        "type": "string",
        "name": "HEAD_ARGS",
        "hasMultipleValues": true
    }]
}'

```
### 4.2 返回结果  
1.创建成功  
状态码:  `200`  
返回信息:  
```
{
    "msg":"create datasource1 successfully"
}
```
2.非法请求:  
状态码:400  
返回信息:  
```
{
    "error":"DataSource[datasource1] already exists"
}
```
3.内部服务器错误    
状态码:500  
返回信息:  
```
{
    "error":"Create DataSource[datasource1] failed : {detail exception message}"
}
```

## 5.获取数据源列表接口(包含segment详细信息)
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources?full=1`  
返回结果:
```
[
    {
        "name": "datasource1",
        "properties": {
            "client": "side"
        },
        "segments": [
            {
                "dataSource": "datasource1",
                "interval": "1000-01-01T00:00:00.000Z/3000-01-01T00:00:00.000Z",
                "version": "0",
                "loadSpec": {
                    "type": "hyper",
                    "path": "/uindex211/datasource1/10000101T000000.000Z_30000101T000000.000Z/0/1",
                    "loadMode": "W"
                },
                "dimensions": "",
                "metrics": "",
                "shardSpec": {
                    "type": "numbered",
                    "partitionNum": 1,
                    "partitions": 2
                },
                "binaryVersion": 9,
                "size": 303,
                "identifier": "datasource1_1000-01-01T00:00:00.000Z_3000-01-01T00:00:00.000Z_0_1"
            },
            ......
        ]
    },
    ......
]
```

## 6.获取数据源列表接口(只包含segment数量)
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources?simple=1`  
返回结果:
```
[
    {
        "name": "datasource1",
        "tiers": {
            "_default_tier": {
                "size": 606,
                "segmentCount": 2
            }
        }
    }
    ......
]
```

## 7.获取指定数据源信息
目前提供了两个接口用于查看数据源的信息  
### 7.1 第一种获取指定数据源信息的接口
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/spec/{datasourceName}`  

返回结果如下所示:
```
{
    "partitions": 2,
    "primaryColumn": "distinct_id",
    "delimiter": "\u0001",
    "columns": [
        "distinct_id",
        "ua_sugo_ip",
        "ua_device_code"
    ],
    "multiValues": [
        false,
        false,
        false
    ],
    "primaryIndex": 0
}
```
`partitions`:分片数  
`primaryColumn`:主键列  
`delimiter`:列分隔符  
`columns`:包含的列名  
`multiValues`:列是否是多值列  
`primaryIndex`:主键列的索引,以上主键列为`distinct_id`,在`columns`中的索引为0.  

### 7.2 第二种获取指定数据源信息接口(返回的信息与创建数据源时一样)
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/dimensions/{datasourceName}`  
返回信息如下:
```
{
    "datasourceName": "datasource1",
    "partitions": 4,
    "columnDelimiter": "\u0001",
    "shardSpec": {
        "type": "single",
        "dimension": "app_id",
        "start": null,
        "end": null,
        "partitionNum": -1
    },
    "dimensions": [
        {
            "type": "string",
            "name": "app_id",
            "hasMultipleValues": false
        },
        {
            "type": "string",
            "name": "event_id",
            "hasMultipleValues": false
        },
        ......
    ]
}
```

## 8.查询所有数据源所有`segment`所在的`regionServer`
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/serverview`  
返回结果如下:
```
{
    "datasource-1": {
        "0": [
            "192.168.0.1:8087",
            "192.168.0.2:8087"
        ],
        ......
    },
    "datasource-2": {
        "0": [
            "192.168.0.2:8087",
            "192.168.0.1:8087"
        ],
        ......
    }
    ......
}
```

## 9.查询指定数据源下所有`segment`所在的`regionServer`
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/serverview/{datasourceName}`  
返回结果如下:
```
{
    "0": [
        "192.168.0.1:8087",
        "192.168.0.2:8087"
    ],
    ......
}
```


## 10.查询指定数据源下所有`segment`的信息
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/segments/{datasourceName}`  
返回结果如下:
```
[
    {
        "partition": 0,
        "version": "0",
        "interval": "1000-01-01T00:00:00.000Z/3000-01-01T00:00:00.000Z",
        "servers": [
            "192.168.0.2:8087",
            "192.168.0.1:8087"
        ],
        "readCount": 1
    },
    ......
]
```
`partition`:分片号  
`version`:默认都是0  
`interval`:默认都是`1000/3000`  
`servers`:分片所在的regionServer  
`readCount`:只读的副本数  
* 如果servers中返回的节点数等于readCount,说明都是只读的副本,没有可写的副本.
* 如果servers中返回了两个节点,而readCount的值为1,说明第一个是可写的副本,第二个是只读的副本
* 如果servers中只返回了一个节点
    > 如果`readCount`值为0,说明第一个为可写的副本  
    > 如果`readCount`值为1,说明没有可写的副本,只有一个只读的副本

## 11.查询指定数据源下所有`segment`只读副本所在的`regionServer`
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/serverview/readonly/{datasourceName}`   
返回结果如下:
```
{
    "0": [
        "192.168.0.1:8087"
    ],
    "1": [
        "192.168.0.2:8087"
    ]
}
```
## 12.查询指定数据源下所有`segment`可写副本所在的`regionServer`
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/serverview/writable/{datasourceName}`   
返回结果如下:
```
{
    "0": [
        "192.168.0.2:8087"
    ],
    "1": [
        "192.168.0.1:8087"
    ]
}
```
