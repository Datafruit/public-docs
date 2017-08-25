# kafka动态维接入

> ## 概要　　
> * 本流程主要演示使用动态维把 kafka 上的 `json` 数据导入到 Druid。  
> * `json` 数据样式： `{"s|str":"dlo6ic","d|dt":1500714407574,"i|index":169}` （`d|dt` 的类型为 `date` ,格式为`millis`）  
> * 使用动态维的规则 ： key的信息包括两部分内容，字段类型+字段名称，字段类型与字段名称以|分割，多个组合以，分割。  
> * 字段类型映射：  
> `i`代表 int；  
> `s`代表 string；  
> `l`代表 long；  
> `d`代表 date；  
> `f`代表 float； 


> 前提：
  > 1. 部署好druid平台环境
  > 2. kafka集群已经部署好，数据已经正常写入特定的topic
  > 3. druid集群服务所在网络与kafka集群所在网络是相通的

## 第一步：编辑json文件
从 kafka 加载数据到 druid 平台，需要通过发送一个 http post 请求到 druid 接入接口。

该 post 请求中包含 json 格式的请求数据信息。为了方便修改，建议先编辑一个 json 文件。

## 第二步：建立Supervisor
使用 curl 命令发送 post 请求。假设 json 文件名为 wiki.json，curl 命令如下：

```shell
  curl -X POST -H 'Content-Type: application/json' -d @/tmp/json/wiki.json http://overlord_ip:8090/druid/indexer/v1/supervisor
```
> **/tmp/json/wiki.json：** 详见[`wiki.json`](#json)  
> **overlord_ip：** 为druid的overlord节点ip地址  

也可以通过网页来操作：  
1. 登录 overlord_ip:8090/supervisor.html  
![](/assets/createSupervisor/top.png)  
2. 删除页面上原来文本框里的内容，将[`wiki.json`](#json)的内容复制，粘贴到文本框中，点击下面的 `Create Supervisor`，即可创建`Supervisor`。  
![](/assets/createSupervisor/textBox.png) 
  
## 第三步：查看Task执行情况
1. 查看日志
- 访问：`http://{OverlordIP}:8090/console.html`，点击 `Task` 的日志，查看 `Task` 的执行情况

   > **OverlordIP:** druid的overlord节点ip地址
   
   ![](/assets/LuceneIndexTaskPics/log.jpg)

2. 查看执行结果
- 使用 `sugo-plyql` 查询 `Task` 的执行结果，具体的命令格式为：
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

## <a id="json"></a> wiki.json文件内容：

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
                    "column":"d|dt",
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
- **`ioConfig.taskDuration:`** 任务持续时间，超过指定时间后，任务会停止接收数据，等数据持久化之后会创建新的任务进程。可设置的格式：一分钟：`PT60S`, 十分钟：`PT10M`, 一天：`P1D`  
- **`ioConfig.consumerProperties:`** kafka消费端接口的配置，比如kafka的服务器配置  
- **`ioConfig.startDelay:`** 开始时延
- **`ioConfig.useEarliestOffset:`** 从kafka的最早的`offset`开始消费 
- **`ioConfig.completionTimeout:`** 完成超时时间（秒）
