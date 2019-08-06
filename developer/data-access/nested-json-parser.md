# 解析嵌套json格式数据

> ## 概要　　
> * 本流程主要介绍如何配置解析嵌套json格式数据.
> * 详细supervisor或task配置介绍可参考相关资料,本文不做过多介绍.

> 前提：
  > 1. 部署好druid平台环境
  > 2. kafka集群已经部署好，数据已经正常写入特定的topic
  > 3. druid集群服务所在网络与kafka集群所在网络是相通的

## 嵌套json格式数据如下
```
{
    "fromhost-ip":"192.168.0.1",
    "hostname":"test1.sugo.io",
    "msg":{
        "EventTime":"2019-01-01 12:30:00",
        "Hostname":"ubuntu"
    }
}
```
## 针对上述json配置的supervisor信息如下
```
{
    "type": "lucene_supervisor",
    "dataSchema": {
        "dataSource": "nested4",
        "parser": {
            "type": "string",
            "parseSpec": {
                "format": "nested",
                "decollator": ".",
                "timestampSpec": {
                    "reNewTime": "true"
                },
                "dimensionsSpec": {
                    "dimensions": [
                        {
                            "name": "fromhost-ip",
                            "type": "string"
                        },
                        {
                            "name": "hostname",
                            "type": "string"
                        },
                        {
                            "name": "EventTime",
                            "type": "string"
                        },
                        {
                            "name": "Hostname1",
                            "type": "string"
                        }
                    ]
                },
                "parseSpec": {
                    "format": "json",
                    "flattenSpec": {
                        "useFieldDiscovery": false,
                        "fields": [{
                                "type": "ROOT",
                                "name": "fromhost-ip"
                            },{
                                "type": "PATH",
                                "name": "hostname",
                                "expr": "$.hostname"
                            },{
                                "type": "PATH",
                                "name": "EventTime",
                                "expr": "$.msg.EventTime"
                            },{
                                "type": "PATH",
                                "name": "Hostname1",
                                "expr": "$.msg.Hostname"
                            }
                        ]
                    }
                }
            }
        },
        "granularitySpec": {
            "type": "uniform",
            "segmentGranularity": "DAY"
        }
    },
    "tuningConfig": {
        "type": "kafka",
        "maxRowsInMemory": 500000,
        "maxRowsPerSegment": 20000000,
        "taskDealRowCount": 100000000,
        "consumerThreadCount": 1
    },
    "ioConfig": {
        "topic": "winsys_log_nest_json",
        "replicas": 1,
        "taskCount": 1,
        "taskDuration": "PT86400S",
        "consumerProperties": {
            "bootstrap.servers": "192.168.0.223:9092,192.168.0.224:9092,192.168.0.225:9092"
        },
        "useEarliestOffset": true
    },
    "suspended": false
}
```
- **`type`** 从kafka加载数据到druid时固定配置为lucene_supervisor
- **`dataSchema.parser.type`** 默认的parser类型为string,所以此处可省略.
- **`dataSchema.parser.parseSpec.format`** parse类型配置为嵌套的"nested".
- **`dataSchema.parser.parseSpec.decollator`** 分割符默认为".",在本文档是将嵌套的json打平,所以此配置无效.如果想将嵌套的json解析后,保留原来的嵌套格式,比如解析以上json后显示的字段包含"msg.EventTime",则需要此配置.
- **`dataSchema.parser.parseSpec.timestampSpec`** 配置时间错列,如果数据中没有时间列,可使用当前时间作为时间列.
- **`dataSchema.parser.parseSpec.timestampSpec.reNewTime`** 使用当前时间作为时间戳.如果使用该配置,其他项不需要配.
- **`dataSchema.parser.parseSpec.timestampSpec.missingValue`** 使用固定时间作为时间戳,比如`2016-08-03T12:53:51.999Z`.如果使用该配置,其他项不需要配.
- **`dataSchema.parser.parseSpec.timestampSpec.column`** 时间戳列.
- **`dataSchema.parser.parseSpec.timestampSpec.format`** 时间格式类型：推荐`millis`  
	> `yy-MM-dd HH:mm:ss`: 自定义的时间格式  
	> `auto`: 自动识别时间，支持iso和millis格式  
  > `iso`：iso标准时间格式，如”2016-08-03T12:53:51.999Z”  
  > `posix`：从1970年1月1日开始所经过的秒数,10位的数字  
  > `millis`：从1970年1月1日开始所经过的毫秒数，13位数字  
- **`dataSchema.parser.parseSpec.dimensionsSpec`** 配置解析后的维度信息.
- **`dataSchema.parser.parseSpec.dimensionsSpec.dimensions`** 此处需要定义所有的维度信息,没在此处定义的维度数据将会被丢弃.注意不能存在相同名称的维度,即使是大小写的区别也不可以.
- **`dataSchema.parser.parseSpec.parseSpec`** 配置解析嵌套json的解析器.
- **`dataSchema.parser.parseSpec.parseSpec.format`** 此处配置为json.
- **`dataSchema.parser.parseSpec.parseSpec.flattenSpec`** 配置json解析信息.
- **`dataSchema.parser.parseSpec.parseSpec.flattenSpec.useFieldDiscovery`** 使用自动发现json根节点下的信息,如果配置为true,将自动解析到上述json中的`fromhost-ip`和`hostname`.而不需要在`fields`中配置.
- **`dataSchema.parser.parseSpec.parseSpec.flattenSpec.fields`** 提取嵌套json中的信息.
- **`dataSchema.parser.parseSpec.parseSpec.flattenSpec.fields.type`** 提取的方式,可选`PATH`或`ROOT`.
	- **`PATH`** 以jsonpath方式提取信息.
	- **`ROOT`** 从根节点提取信息,比如`fromhost-ip`.
- **`dataSchema.parser.parseSpec.parseSpec.flattenSpec.fields.name`** json中的key,比如`hostname`.
- **`dataSchema.parser.parseSpec.parseSpec.flattenSpec.fields.expr`** 当`type`为`PATH`时有效,具体格式可参考jsonpath,比如`$.msg.EventTime`.

- **`dataSchema.granularitySpec.type:`** 默认使用`uniform`
- **`dataSchema.granularitySpec.segmentGranularity:`** 段粒度，根据每天的数据量进行设置。
小数据量建议`DAY`，大数据量（每天百亿）可以选择`HOUR`。可选项：`SECOND`、`MINUTE`、`FIVE_MINUTE`、`TEN_MINUTE`、`FIFTEEN_MINUTE`、`HOUR`、`SIX_HOUR`、`DAY`、`MONTH`、`YEAR`。

- **`tuningConfig.type:`** 设置为`kafka`
- **`tuningConfig.taskDealRowCount:`** 单`task`处理的最大记录数,超过该限制后,将创建新的task进行处理.
- **`ioConfig.topic:`** kafka中的topic名  
- **`ioConfig.consumerProperties:`** kafka消费端接口的配置，比如kafka的服务器配置  
- **`taskCount:`** 启动的任务进程数  
- **`replicas:`** 任务的副本数  
- **`taskDuration:`** 任务持续时间，超过指定时间后，任务会停止接收数据，等数据持久化之后会创建新的任务进程。可设置的格式：一分钟：`PT60S`, 十分钟：`PT10M`, 一天：`P1D`  
- **`useEarliestOffset:`** 从kafka的最早的`offset`开始消费  
