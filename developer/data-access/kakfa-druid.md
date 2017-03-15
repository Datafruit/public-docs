# 从kafka加载数据到druid

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

Json文件格式：

```javascript
{
  "type": "lucene_supervisor",
  "dataSchema": {
    "dataSource": "wiki",
    "parser": {
      "type": "string",
      "parseSpec": {
        "format": "url",
        "timestampSpec": {
          "column": "the_date",
          "format": "millis"
        },
        "dimensionsSpec": {
          "dimensions": [
            {
              "name": "name",
              "type": "string"
            },    			
            {
              "name": "age",
              "type": "int"
            },
            {
              "name": "score",
              "type": "float"
            },
            {
              "name": "create_time",
              "type": "datetime"
            }
          ],
          "dimensionExclusions": [],
          "spatialDimensions": []
        }
      }
    },
    "metricsSpec": [],
    "granularitySpec": {
      "type": "uniform",
      "segmentGranularity": "YEAR",
      "queryGranularity": "NONE"
    }
  },
  "tuningConfig": {
    "type": "kafka",
    "maxRowsInMemory": 500000,
    "maxRowsPerSegment": 20000000,
    "intermediatePersistPeriod": "PT20M",
    "basePersistDirectory": "/data/druidTask/storage/wiki",
    "buildV9Directly": true
  },
  "ioConfig": {
    "topic": "wiki",
    "consumerProperties": {
      "bootstrap.servers": "192.168.0.101:9092,192.168.0.102:9092,192.168.0.103:9092"
    },
    "taskCount": 1,
    "replicas": 1,
    "taskDuration": "PT3600S",
    "useEarliestOffset": "true"
  }
}
```

- **`type:`** 从kafka加载数据到druid时固定配置为lucene_supervisor
- **`dataSchema.dataSource`**  数据源的名称，类似关系数据库中的表名
- **`dataSchema.parser.type`**  固定string
- **`dataSchema.parser.parseSpec.format`** kafka中单条数据的格式，比如`url/json/csv/tsv/hive`。

- **`url:`** 数据类似`url`参数串，如：`name=jack&age=21&score=78.5&the_date= 1489131528601`

- **`dataSchema.parser.parseSpec.timestampSpec:`** 时间戳列以及时间的格式  
- **`dataSchema.parser.parseSpec.timestampSpec.format`** 时间格式类型：推荐`millis`  
	> `yy-MM-dd HH:mm:ss`: 自定义的时间格式  
	> `auto`: 自动识别时间，支持iso和millis格式  
  > `iso`：iso标准时间格式，如”2016-08-03T12:53:51.999Z”  
  > `posix`：从1970年1月1日开始所经过的秒数,10位的数字  
  > `millis`：从1970年1月1日开始所经过的毫秒数，13位数字  
- **`dataSchema.parser.parseSpec.dimensionsSpec.dimensions:`** 维度定义列表，每个维度的格式为：`{“name”: “age”, “type”:”string”}`。Type支持的类型：`string`、`int`、`float`、`long`、`datetime`  

- **`json:`** json格式数据
	- 数据格式：`{"name":"jack","age": 21,"score": 78.5,"the_date": 1489131528601}`

- **`csv：`** csv格式数据  
	- **`dataSchema.parser.parseSpec.listDelimiter：`** 分隔符
	- **`dataSchema.parser.parseSpec.columns：`** 维度列表，格式：`["name","age","score"]`  

- **`hive：`** hive数据文件
	- **`dataSchema.parser.parseSpec.timestampSpec`**
	- **`dataSchema.parser.parseSpec.dimensionsSpec`**
	- **`dataSchema.parser.parseSpec.separator:`** 维度分隔符
	- **`dataSchema.parser.parseSpec.columns:`** 维度列表

- **`dataSchema.granularitySpec.type:`** 默认使用`uniform`
- **`dataSchema.granularitySpec.segmentGranularity:`** 段粒度，根据每天的数据量进行设置。
小数据量建议`DAY`，大数据量（每天百亿）可以选择`HOUR`。可选项：`SECOND`、`MINUTE`、`FIVE_MINUTE`、`TEN_MINUTE`、`FIFTEEN_MINUTE`、`HOUR`、`SIX_HOUR`、`DAY`、`MONTH`、`YEAR`。

- **`tuningConfig.type:`** 设置为`kafka`
- **`tuningConfig.basePersistDirectory:`** 任务中接收到的数据临时存放路径，需要注意用户的读写权限。
- **`ioConfig.topic:`** kafka中的topic名  
- **`ioConfig.consumerProperties:`** kafka消费端接口的配置，比如kafka的服务器配置  
- **`taskCount:`** 启动的任务进程数  
- **`replicas:`** 任务的副本数  
- **`taskDuration:`** 任务持续时间，超过指定时间后，任务会停止接收数据，等数据持久化之后会创建新的任务进程。可设置的格式：一分钟：`PT60S`, 十分钟：`PT10M`, 一天：`PT1D`  
- **`useEarliestOffset:`** 从kafka的最早的`offset`开始消费  
