# 直接从csv将数据导入druid后台

> ## 概要　　
> 本流程主要演示把本地的`csv`文件数据实时导入到`Druid`

## 导入CSV过程步骤：

![](/assets/datacsv/datacsv1.png)

以下以北京基调网络股份有限公司为例，从 cvs 导入到 csv 落地全过程操作指引：

## 第一步：找MiddleManagers服务IP

![](/assets/datacsv/datacsv2.png)

## 第二步：到MiddleManagers服务器上上传csv、json文件

![](/assets/datacsv/datacsv3.png)

- 通过shell登录后，输入命令：cd  /data1/tmp/druid   
> 注: /data1/tmp/druid 根据 taskspec.json 文件中指定的路径  

## 创建、编辑、保存taskspec.json文件说明：  
在 data1/tmp/druid 目录下，vim taskspec.json ，将下面json配置说明内容拷贝，然后点击键盘 ESC 按键退出编辑， 再输入 wq 命令退出并保存。

![](/assets/datacsv/datacsv4.png)

### 编辑taskspec.json文件中data类型说明：

 1. 定义taskspec.json文件中的时间戳格式

- csv文件导入有时间列，定义时间戳要求：  
 **`timestampSpec:`** {"column": "ts","format": "yyyyMM"},  
- csv文件导入没有时间列，定义时间戳要求：  
 **`timestampSpec:`** {"column": "ts","format": "yyyyMM"},改成  "timestampSpec": {”missingValue”: "2017-03-12T12:00:00Z"},  
2. 时间格式类型要求说明：  
  - CSV文件导入数据源有date类型，要对taskspec.json文件date类型按要求定义：
    - Date类型格式2017-01-01 00:00，则定义为："date","format":"yy-MM-dd HH:mm"
    - Date类型格式2017-01-01 00:00:00，则定义为："date","format":"yy-MM-dd HH:mm:ss"
    - Date类型格式2017-01-01，则定义为："date","format":"yy-MM-dd"
    - Date类型格式2017-01，则定义为："date","format":"yyyyMM"
    - Date类型格式2017，则定义为："date","format":"yyyy"
    - Date类型格式是从1970年1月1日开始所经过的秒数,10位的数字，则定义为："date","format":"posix"
    - Date类型格式是从1970年1月1日开始所经过的毫秒数，13位数字，则定义为："date","format":"millis"

![](/assets/datacsv/datacsv5.png)

## 第三步：执行命令上传csv文件

![](/assets/datacsv/datacsv6.png)

csv文件上传， 在shell工具登录：MiddleManagers服务器， cd  /data1/tmp/druid  目录下，执行：

  ```shell
  curl -X 'POST' -H 'Content-Type:application/json' -d @task-spec.json http://{OverlordIP}:8090/druid/indexer/v1/task
  ```

   > **overlordIP:** druid的overlord节点ip地址

   > **task-spec.json** task配置文件，详见下文

## 第四步：查看csv文件上传task的日志信息。进入overlord服务的任务列表页面，查看数据导入情况。

![](/assets/datacsv/datacsv7.png)

  ```javascript
  http://{overlordIP}:8090/console.html
  ```

  > **overlordIP:** druid的overlord节点ip地址

## 第五步：在需要停止task时，可以发送如下http post请求停止task任务

  ```shell
  curl -X 'POST' -H 'Content-Type:application/json' http://{overlordIP}:8090/druid/indexer/v1/task/{taskId}/shutdown
  ```

  > **overlordIP:** druid的overlord节点ip地址

  > **taskId:** 在`http://{overlordIP}:8090/console.html`task详细页面对应 **id** 列的信息


### task-spec.json详细配置如下：

```javascript
{
    "type": "lucene_index_realtime",
    "spec": {
        "dataSchema": {
            "metricsSpec": [],
            "parser": {
                "parseSpec": {
                    "format": "csv",
                    "timestampSpec": {"column": "ts","format": "yy-MM-dd HH:mm:ss.SSS"},
                    "dimensionsSpec": {
                        "dimensions": [
                        {"name": "cardNum","type": "string"},
                        {"name": "leftNode","type": "string"},
                        {"name": "rightNode","type": "string"},
                        {"name": "level","type": "int"},
                        {"name": "profit","type": "float"}]
                    },
                    "listDelimiter": ",",
                    "columns": ["ts","cardNum","leftNode","rightNode","level","profit"]
                }
            },
            "granularitySpec": {
                "intervals": ["2001-01-01/2020-01-01"],
                "segmentGranularity": "YEAR",
                "type": "uniform"
            },
            "dataSource": "com_HyoaKhQMl_project_r1U1FDLjg"
        },
        "ioConfig": {
            "firehose": {
                "type": "local",
                "filter": "*.csv",
                "baseDir": "/data1/tmp/druid/"
            },
            "type": "realtime"
        },
        "tuningConfig": {
            "type": "realtime",
            "basePersistDirectory": "/data1/tmp/task/storage/wxj",
            "reportParseExceptions":true,
            "rejectionPolicy": {
                "type": "none"
            },
            "consumerThreadCount": 2
        }
    },
    "context": {
        "debug": true
    }
}

```

- **`spec.dataSchema.parser.parseSpec.timestampSpec.column:`** 时间戳列

- **`spec.dataSchema.parser.parseSpec.timestampSpec.format:时间格式类型:`** 推荐`millis`

  - [ ] **`yy-MM-dd HH:mm:ss.SSS:`** 自定义的时间格式
  - [ ] **`auto:`** 自动识别时间，支持`iso`和`millis`格式
  - [ ] **`iso:`** iso标准时间格式，如`2016-08-03T12:53:51.999Z`
  - [ ] **`posix:`** 从1970年1月1日开始所经过的秒数,10位的数字
  - [ ] **`millis:`** 从1970年1月1日开始所经过的毫秒数，13位数字

- **`spec.dataSchema.parser.parseSpec.dimensionsSpec.dimensions:`** 维度定义列表，每个维度的格式为：```{“name”: “age”, “type”:”string”}```。Type支持的类型：`string`、`int`、`float`、`long`、`date`

- **`spec.dataSchema.parser.parseSpec.listDelimiter:`** csv列分隔符
- **`spec.dataSchema.parser.parseSpec.columns:`** 维度列表，包含时间戳列，`eg:["ts","ProductID"]`
- **`spec.dataSchema.granularitySpec.intervals:`** 数据时间戳范围

- **`spec.dataSchema.granularitySpec.segmentGranularity:`** 段粒度，根据每天的数据量进行设置。

 小数据量建议DAY，大数据量（每天百亿）可以选择`HOUR`。可选项：`SECOND`、`MINUTE`、`FIVE_MINUTE`、`TEN_MINUTE`、`FIFTEEN_MINUTE`、`HOUR`、`SIX_HOUR`、`DAY`、`MONTH`、`YEAR`。

- **`spec.dataSchema.dataSource:`** 数据源的名称，类似关系数据库中的表名

- **`spec.ioConfig.firehose.filter:`** 文件名匹配符, **`*.csv`** 匹配 **`csv`** 文件
- **`spec.ioConfig.firehose.baseDir:`** 数据文件所在目录
- **`spec.ioConfig.firehose.parser:`** 与上面配置的 **`spec.dataSchema.parser`** 一样
- **`spec.tuningConfig.windowPeriod:`** 数据时间窗口，不需要时间窗口则可以不配置
- **`spec.tuningConfig.rejectionPolicy:`** 数据过滤策略，如果不过滤则不需要修改
- **`spec.tuningConfig.basePersistDirectory:`** 任务中接收到的数据临时存放路径，需要注意用户的读写权限。
- **`context.debug:`** 开启`debug`模式，调试时开启，生成环境不开启

