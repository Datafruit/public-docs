# 存在则更新不存在则新增task:lucene_upset
task类型: `lucene_upset`  
## task说明:  
+ `lucene_upset`类型的task支持行级数据的新增,修改和删除,由`actionColumn`指定特定列,该列的数据将标识行的记录是执行新增,修改或删除操作.该列的值只允许`a`,`u`,`d`三个值,分别代表新增,修改,删除操作.  
+ `lucene_upset`类型的task实现了UPSET特性,即如果存在则更新,如果不存在则新增.是否存在是根据指定的列值进行判断,通常是主键,比如订单表的主键是订单号orderid,则根据orderid判断记录是否存在.  
+ `granularitySpec.intervals`:指定了数据允许的最早和最晚时间,如果早于最早时间或晚于最晚时间则丢弃该行数据.
## 参数解析
- `filterColumns`: 指定主键列  
- `actionColumn`: 指定操作列,比如`action`
- `worker`: 制定task运行的worker节点，需要使用域名，或参考task管理界面的`Remote Workers`表格
- `dataSchema`: 参考其他task的配置
- `tuningConfig`: 可以不指定,使用默认配置.注意不能同时指定`maxRowsPerSegment`和`numShards`.建议通过配置将segment大小控制在300MB到600MB之间.
- `tuningConfig.maxRowsPerSegment`: 指定每个segment的最大记录条数,建议每个segment内的记录数不超过500万,不低于100万.应根据字段数进行适当调整.比如小与30个字段配置400万,大于100个字段配置100万.
- `tuningConfig.numShards`: 指定一个数据粒度内的分片数,比如每天数据量为1kw数据,字段数不超50,设置`numShards`为3.如果以天为粒度,每天将产生3个segment,每个segment三四百万条记录.
- `tuningConfig.overwrite`: 是否覆盖相同周期内的旧数据.默认为`false`,如果要设置为`true`,请慎重并咨询专业人员.
- `tuningConfig.reportParseExceptions`: 是否打印没被处理的数据,用于数据开发调试过程,默认为false.
- `writerConfig`: 默认不指定,使用默认配置即可.
- `ioConfig`: 指定firehose的类型,支持本地文件,hdfs文件接入.
- `context.throwAwayBadData`: 是否直接丢弃解析出错的数据而不停止task.默认为false,即如果遇到解析报错的数据,则停止task.如果确定只是少量数据有问题,并且直接丢弃也不影响业务,则可设置为true(请慎重).
- `context.disableUpSet`:是否要停用upset特性.默认为false.如果停用,如果设置action为修改(`u`),但原有数据中找不到对应的记录,则丢弃数据,而不采用新增方式插入数据,即不执行insert操作.
## 示例json配置
```
{
    "type": "lucene_upset",
    "filterColumns": [
        "old_card_no"
    ],
    "worker":"node2.sugo.io:8091",
    "actionColumn": "action",
    "dataSchema": {
        "dataSource": "janpy_upset1",
        "parser": {
            "parseSpec": {
                "format": "tsv",
                "timestampSpec": {
                    "column": "dealtime",
                    "format": "yy-MM-dd HH:mm:ss.SSS"
                },
                "dimensionsSpec": {
                    "dimensionExclusions": [],
                    "spatialDimensions": [],
                    "dimensions": [
                        {
                            "name": "old_card_no",
                            "type": "string"
                        },
                        {
                            "name": "gender",
                            "type": "string"
                        },
                        {
                            "name": "birthday",
                            "type": "string"
                        },
                        {
                            "name": "mobile_province",
                            "type": "string"
                        },
                        {
                            "name": "mobile_city",
                            "type": "string"
                        },
                        {
                            "name": "province_cd",
                            "type": "string"
                        },
                        {
                            "name": "city_cd",
                            "type": "string"
                        },
                        {
                            "name": "member_type_id",
                            "type": "string"
                        },
                        {
                            "name": "is_papersno",
                            "type": "string"
                        },
                        {
                            "name": "first_create_datetime",
                            "type": "string"
                        },
                        {
                            "name": "card_status_cd",
                            "type": "string"
                        },
                        {
                            "name": "member_point",
                            "type": "int"
                        },
                        {
                            "name": "member_regsource",
                            "type": "string"
                        },
                        {
                            "name": "member_identifier",
                            "type": "string"
                        },
                        {
                            "name": "open_card_type_cd",
                            "type": "string"
                        },
                        {
                            "name": "dealtime",
                            "type": "date",
                            "format": "yy-MM-dd HH:mm:ss.SSS"
                        },
                        {
                            "name": "is_email_check",
                            "type": "string"
                        },
                        {
                            "name": "is_mobile_check_cd",
                            "type": "string"
                        },
                        {
                            "name": "upgrade_times",
                            "type": "date",
                            "format": "yy-MM-dd HH:mm:ss.SSS"
                        },
                        {
                            "name": "inreg_source",
                            "type": "string"
                        },
                        {
                            "name": "credit_point",
                            "type": "string"
                        },
                        {
                            "name": "action",
                            "type": "string"
                        }
                    ]
                },
                "delimiter": "\u0001",
                "listDelimiter": "\u0002",
                "columns": [
                    "old_card_no",
                    "gender",
                    "birthday",
                    "mobile_province",
                    "mobile_city",
                    "province_cd",
                    "city_cd",
                    "member_type_id",
                    "is_papersno",
                    "first_create_datetime",
                    "card_status_cd",
                    "member_point",
                    "member_regsource",
                    "member_identifier",
                    "open_card_type_cd",
                    "dealtime",
                    "is_email_check",
                    "is_mobile_check_cd",
                    "upgrade_times",
                    "inreg_source",
                    "credit_point",
                    "action"
                ]
            }
        },
        "metricsSpec": [],
        "granularitySpec": {
            "type": "uniform",
            "segmentGranularity": "DAY",
            "queryGranularity": {
                "type": "none"
            },
            "rollup": false,
            "intervals": [
                "1000-01-01T00:00:00.000Z/3000-01-01T00:00:00.000Z"
            ]
        }
    },
    "tuningConfig": {
        "type": "lucene_index",
        "maxRowsPerSegment": 5000000,
        "numShards": -1,
        "basePersistDirectory": null,
        "overwrite": false,
        "reportParseExceptions": false
    },
    "writerConfig": {
        "type": "lucene",
        "maxBufferedDocs": -1,
        "ramBufferSizeMB": 16,
        "indexRefreshIntervalSeconds": 6,
        "isIndexMerge": true,
        "mergedNum": 1,
        "isCompound": false,
        "maxMergeAtOnce": 5,
        "maxMergedSegmentMB": 5120,
        "maxMergesThreads": 1,
        "mergeSegmentsPerTire": 10,
        "writeThreads": 5,
        "limiterMBPerSec": 0,
        "useDefaultLockFactory": false
    },
    "ioConfig": {
        "type": "lucene_index",
        "firehose": {
            "type": "hdfs",
            "baseDir": "/user/hive/warehouse/test_tindex2_10",
            "filter": "*",
            "parser": null
        }
    },
    "context": {
        "debug": true,
        "throwAwayBadData": true,
        "disableUpSet": true
    }
}
```

