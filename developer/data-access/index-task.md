# `LuceneIndexTask`　接口使用流程

以下以接入 `csv` 数据为例。

## 第一步：准备好目标 `csv` 文件数据

- 本例的部分`csv`数据如下：
```
1500520434191,1,jkcdashjfksa
1500520430411,2,fdfjdkhjfk
1500020434191,4,fkdsjfkskjfa
1420520434191,5,kljfdsaj
1500510434191,9,sagfkldjgf
1500520214191,8,fdjsklklf
```
## 第二步创建、编辑、保存 `json` 文件

- 具体的json文件配置请参考后文的[LuceneIndexTask_Json实例](#LuceneIndexTask_instance)

## 第三步：上传 `csv`、`json` 文件到`MiddleManager`服务器上
- 使用`scp`命令将`csv`、`json`文件上传到`MiddleManager`
```shell
scp {file_path}  root@{MiddleManagerIP}:{storage_dir}
```
   > **file_path** `csv` 或 `json` 文件在本地的绝对路径，要指定到文件名这一层  
   > **MiddleManagerIP:** druid的 `MiddleManager` 节点ip地址  
   > **storage_dir** 在 `MiddleManager` 的存放目录,使用绝对路径

## 第四步：执行命令，启动 `Task`
1. 通过 `ssh` 远程登录到第三步中选择的 `MiddleManager` 中
2. 执行 `cd` 命令进入第三步中选择存放 `json` 文件的目录中
3. 执行 `curl`命令启动 `Task`,具体命令格式如下：
```shell
curl -X 'POST' -H 'Content-Type:application/json' -d @{file_name} http://{OverlordIP}:8090/druid/indexer/v1/task
```
   > **OverlordIP:** druid的overlord节点ip地址

   > **file_name** `json` 文件名


## 第五步：查看 `Task` 执行情况
1. 查看日志
- 访问：`http://{OverlordIP}:8090/console.html` ,点击 `Task` 的日志，查看 `Task` 的执行情况

   > **OverlordIP:** druid的overlord节点ip地址

2. 查看执行结果
- 使用 `sugo-plyql` 查询 `Task` 的执行结果，具体的命令格式为：
```shell
./plyql -h {OverlordIP} -q 'select count(*) from {datasource}' -v
```
   > **OverlordIP:** druid的overlord节点ip地址  
   > **datasource:** `json` 配置文件中定义的 `datasource` 名称
   
   关于 `sugo-plyql` 的安装和使用，详见[ sugo-plyql 使用文档](/developer/interfaces/sugo-plyql.md)
   
### <a id="LuceneIndexTask_instance"></a>  本流程使用的 `json` 配置实例如下：

```json
{
    "type": "lucene_index",
    "worker": "dev224.sugo.net:8091",
    "spec": {
        "dataSchema": {
            "dataSource": "index-224-cyz113",
            "metricsSpec": [],
            "parser": {
                "parseSpec": {
                    "format": "csv",
                    "timestampSpec": {"column": "da","format": "millis"},
                    "dimensionsSpec": {
                        "dimensionExclusions": [],
                        "spatialDimensions": [],
                        "dimensions": [
                            {"name": "num","type": "int"},
                            {"name": "strvalue","type": "string"}                                       
                            ]
                        },
                        "listDelimiter": ",",
                        "columns": ["da","num","strvalue"]
                    }
            },
            "granularitySpec": {
                "intervals": ["2010-01-01/2018-01-01"],
                "segmentGranularity": "DAY",
                "queryGranularity": "NONE",
                "type": "uniform"
            }
        },
        "ioConfig": {
            "type":"lucene_index",
            "firehose":{
                "type": "local",
                "filter": "data1.csv",
                "baseDir": "/data1/csv/"
            }
        },
        "tuningConfig": {
            "type": "lucene_index",
            "reportParseExceptions":true
        }
    },
    "context":{
        "debug":true
    }
}
```
- **`type:`** 指定数据接入的类型,固定为`lucene_index`
- **`worker:`** 指定具体的worker,一般的形式为`ip:port`
- **`spec:`** 一些参数说明
- **`spec.dataSchema:`** 关于接入数据的概要说明
- **`spec.dataSchema.datasource:`** 数据源的名称，类似关系数据库中的表名
- **`spec.dataSchema.metricsSpec:`** 所有的指标列和所使用的聚合函数,可以不设置
- **`spec.dataSchema.parser:`** 数据转换器
- **`spec.dataSchema.parser.parseSpec:`** 数据转换的参数说明
- **`spec.dataSchema.parser.parseSpec.format:`** 单条数据的格式
- **`spec.dataSchema.parser.parseSpec.timestampSpec:`** 时间戳的说明
- **`spec.dataSchema.parser.parseSpec.timestampSpec.column:`** 时间戳的列名
- **`spec.dataSchema.parser.parseSpec.timestampSpec.format:`** **时间格式类型**， 推荐 `millis`   
  - [ ] **`yy-MM-dd HH:mm:ss.SSS:`** 自定义的时间格式
  - [ ] **`auto:`** 自动识别时间，支持`iso`和`millis`格式
  - [ ] **`iso:`** iso标准时间格式，如`2016-08-03T12:53:51.999Z`
  - [ ] **`posix:`** 从1970年1月1日开始所经过的秒数,10位的数字
  - [ ] **`millis:`** 从1970年1月1日开始所经过的毫秒数，13位数字

- **`spec.dataSchema.parser.parseSpec.dimensionsSpec:`** 维度说明
- **`spec.dataSchema.parser.parseSpec.dimensionsSpec.dimensionExclusions:`** 排除在外的维度

- **`spec.dataSchema.parser.parseSpec.dimensionsSpec.spatialDimensions:`** 维度的空间说明

- **`spec.dataSchema.parser.parseSpec.dimensionsSpec.dimensions:`** 维度定义列表，每个维度的格式为： `{“name”: “age”, “type”:”string”}` 。Type支持的类型：`string`、`int`、`float`、`long`、`date`
- **`spec.dataSchema.parser.parseSpec.dimensionsSpec.listDelimiter:`**  csv列分隔符
- **`spec.dataSchema.parser.parseSpec.dimensionsSpec.columns:`**  维度列表，包含时间戳列，`eg:["da","ProductID"]`
- **`spec.dataSchema.granularitySpec:`** 数据粒度说明
- **`spec.dataSchema.granularitySpec.intervals:`** 数据时间戳范围,可以指定多个范围
- **`spec.dataSchema.granularitySpec.segmentGranularity:`** 段粒度，根据每天的数据量进行设置。 小数据量建议`DAY`，大数据量（每天百亿）可以选择`HOUR`。可选项：`SECOND`、`MINUTE`、`FIVE_MINUTE`、`TEN_MINUTE`、`FIFTEEN_MINUTE`、`HOUR、SIX_HOUR`、`DAY`、`MONTH`、`YEAR`。

- **`spec.dataSchema.granularitySpec.queryGranularity:`**　查询粒度
- **`spec.dataSchema.granularitySpec.type:`** 粒度说明的类型，默认使用 `uniform`

- **`spec.ioConfig:`** 数据的IO说明
- **`spec.ioConfig.type:`** 固定为`lucene_index`
- **`spec.ioConfig.firehose:`** 数据源适配器
- **`spec.ioConfig.firehose.type:`** 数据源适配器的类型,一般用`local`
- **`spec.ioConfig.firehose.filter:`** 文件名匹配符，如`*.csv` 匹配所有的`csv`文件
- **`spec.ioConfig.firehose.parser:`** 数据转换器,与上面配置的 `spec.dataSchema.parser` 一样
- **`spec.ioConfig.firehose.baseDir:`** 数据文件所在目录

- **`spec.tuningConfig:`** 优化说明
- **`spec.tuningConfig.type:`** 固定为`lucene_index`
- **`spec.tuningConfig.reportParseExceptions:`** 是否汇报数据解析错误，默认为`false`
- **`context:`**　任务上下文环境设置，可以不设置

- **`context.debug:`**　开启 `debug` 模式，调试时开启，生成环境不开启
