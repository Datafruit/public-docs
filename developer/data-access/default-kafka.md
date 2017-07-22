# default_kafka接入
> 前提：
  > 1. 部署好druid平台环境
  > 2. kafka集群已经部署好，数据已经正常写入特定的topic
  > 3. druid集群服务所在网络与kafka集群所在网络是相通的

从kafka加载数据到druid平台，需要通过发送一个http post请求到druid接入接口。

该post请求中包含json格式的请求数据信息。为了方便修改，建议先编辑一个json文件，

然后使用curl命令发送post请求。假设json文件名为wiki.json，curl命令如下：

```shell
  curl -X POST -H 'Content-Type: application/json' -d @/tmp/json/wiki.json http://overlord_ip:8090/druid/indexer/v1/supervisor
```

> **/tmp/json/wiki.json：** 详见下文  
> **overlord_ip：** 为druid的overlord节点ip地址  

## wiki.json文件内容：

```
{
    "type":"default_supervisor",
    "dataSchema":{
        "dataSource":"wiki",
        "parser":{
            "parseSpec":{
                "format":"json",
                "dimensionsSpec":{
                    "dynamicDimension":true,
                    "dimensions":[]
                },
                "timestampSpec":{
                    "column":"d|place_time",
                    "format":"millis"
                }
            },
            "type":"single"
        },
        "metricsSpec":[],
        "granularitySpec":{
            "type": "uniform",
            "segmentGranularity": "DAY",
            "queryGranularity": "NONE"
        }
    },
    "tuningConfig":{
        "type":"kafka",
        "maxRowsInMemory":10000000,
        "maxRowsPerSegment":20000000,
        "intermediatePersistPeriod":"PT10M",
        "buildV9Directly":true,
        "reportParseExceptions":false
    },
    "ioConfig":{
        "topic":"wiki",
        "replicas":1,"taskCount":1,"taskDuration":"PT21600S",
        "consumerProperties":{
            "bootstrap.servers":"192.168.0.222:9092,192.168.0.220:9092,192.168.0.221:9092"
        },
        "startDelay":"PT5S",
        "period":"PT30S",
        "useEarliestOffset":true,
        "completionTimeout":"PT1800S",
        "lateMessageRejectionPeriod":null
    },
    "ipLibSpec":{
        "type":"none"
    }
}
```


- **`type:`** 固定配置为`default_supervisor`
- **`dataSchema.dataSource`** 数据源的名称，类似关系数据库中的表名
- **`dataSchema.parser`** 包含如何解析数据的相关内容

- **`dataSchema.metricsSpec`**  所有的指标列和所使用的聚合函数

- **`dataSchema.granularitySpec:`** 数据的存储和查询粒度
- **`dataSchema.granularitySpec.type:`** 默认使用`uniform`
- **`dataSchema.granularitySpec.segmentGranularity:`** 段粒度，根据每天的数据量进行设置  
小数据量建议`DAY`，大数据量（每天百亿）可以选择`HOUR`。可选项：`SECOND`、`MINUTE`、`FIVE_MINUTE`、`TEN_MINUTE`、`FIFTEEN_MINUTE`、`HOUR`、`SIX_HOUR`、`DAY`、`MONTH`、`YEAR`。
- **`dataSchema.granularitySpec.queryGranularity:`** 查询粒度 

- **`tuningConfig:`** 用于优化数据摄取的过程
- **`tuningConfig.type:`** 设置为`kafka`
- **`tuningConfig.maxRowsInMemory:`** 在存盘之前内存中最大的存储行数，指的是聚合后的行数
- **`tuningConfig.maxRowsPerSegment:`** 每个segment最大的存储行数
- **`tuningConfig.reportParseExceptions:`** 是否汇报数据解析错误

- **`ioConfig:`** 指明真正的具体的数据源  
- **`ioConfig.topic:`** kafka中的topic名称 
- **`ioConfig.taskDuration:`** 任务持续时间，超过指定时间后，任务会停止接收数据，等数据持久化之后会创建新的任务进程。可设置的格式：一分钟：`PT60S`, 十分钟：`PT10M`, 一天：`PT1D`  
- **`ioConfig.consumerProperties:`** kafka消费端接口的配置，比如kafka的服务器配置  
- **`ioConfig.startDelay:`** 开始时延
- **`ioConfig.useEarliestOffset:`** 从kafka的最早的`offset`开始消费 
- **`ioConfig.completionTimeout:`** 完成超时时间（秒）
