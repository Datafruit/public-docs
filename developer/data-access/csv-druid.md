# 直接从csv将数据导入druid后台
```
{
	"type": "lucene_index_realtime",
	"spec": {
		"dataSchema": {
			"metricsSpec": [],
			"parser": {
				"parseSpec": {
					"format": "csv",
					"timestampSpec": {"column": "ts","format": "yyyyMM"},
					"dimensionsSpec": {
						"dimensionExclusions": [],
						"spatialDimensions": [],
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
				"queryGranularity": "NONE",
				"type": "uniform"
			},
			"dataSource": "com_HyoaKhQMl_project_r1U1FDLjg"
		},
		"ioConfig": {
			"firehose": {
				"type": "local",
				"filter": "*",
				"baseDir": "/data1/tmp/druid/",
				"parser": {
					"parseSpec": {
						"format": "csv",
						"timestampSpec": {"column": "ts","format": "yyyyMM"},
						"dimensionsSpec": {
							"dimensionExclusions": [],
							"spatialDimensions": [],
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
				}
			},
			"type": "realtime"
		},
		"tuningConfig": {
			"type": "realtime",
			"maxRowsInMemory": 500000,
			"intermediatePersistPeriod": "PT10M",
			"basePersistDirectory": "/data1/tmp/task/storage/wxj",
			"windowPeriod": "PT6000M",
			"rejectionPolicy": {
				"type": "none"
			},
			"buildV9Directly": true,
			"consumerThreadCount": 2
		}
	},
	"context": {
		"debug": true
	}
}
```

- spec.dataSchema.parser.parseSpec.timestampSpec.column:时间戳列

- spec.dataSchema.parser.parseSpec.timestampSpec.format:时间格式类型：推荐millis

- [ ] yy-MM-dd HH:mm:ss: 自定义的时间格式
- [ ] auto: 自动识别时间，支持iso和millis格式
- [ ] iso：iso标准时间格式，如”2016-08-03T12:53:51.999Z”
- [ ] posix：从1970年1月1日开始所经过的秒数,10位的数字
- [ ] millis：从1970年1月1日开始所经过的毫秒数，13位数字

- spec.dataSchema.parser.parseSpec.dimensionsSpec.dimensions: 维度定义列表，每个维度的格式为：{“name”: “age”, “type”:”string”}。Type支持的类型：string、int、float、long、datetime

- spec.dataSchema.parser.parseSpec.listDelimiter: csv列分隔符
- spec.dataSchema.parser.parseSpec.columns: 维度列表，包含时间戳列，eg:["ts","ProductID"]
- spec.dataSchema.granularitySpec.intervals: 数据时间戳范围

- spec.dataSchema.granularitySpec.segmentGranularity：段粒度，根据每天的数据量进行设置。

 小数据量建议DAY，大数据量（每天百亿）可以选择HOUR。可选项：SECOND、MINUTE、FIVE_MINUTE、TEN_MINUTE、FIFTEEN_MINUTE、HOUR、SIX_HOUR、DAY、MONTH、YEAR。

- spec.dataSchema.dataSource：数据源的名称，类似关系数据库中的表名

- spec.ioConfig.firehose.filter： 文件名匹配符,*.csv匹配csv文件
- spec.ioConfig.firehose.baseDir：数据文件所在目录
- spec.ioConfig.firehose.parser： 与上面配置的spec.dataSchema.parser一样
- spec.tuningConfig.windowPeriod: 数据时间窗口，不需要时间窗口则可以不配置
- spec.tuningConfig.rejectionPolicy: 数据过滤策略，如果不过滤则不需要修改
- spec.tuningConfig.basePersistDirectory：任务节点保存数据的临时目录
- context.debug: 开启debug模式，调试时开启，生成环境不开启

