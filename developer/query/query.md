# 查询 Tindex-Query-Json


- [Timeseries](#Timeseries)
- [TopN](#TopN)
- [GroupBy](#GroupBy)
- [Select](#Select)
- [Search](#Search)
- [TimeBoundary](#TimeBoundary)
- [SegmentMetadata](#SegmentMetadata)

&#160; &#160; &#160; &#160;Query，即查询。Druid包含多种查询类型。   
&#160; &#160; &#160; &#160;本文使用一个案例来介绍如何进行查询，以供参考。案例中的广告为一个拼有标识的URL，如：   
&#160; &#160; &#160; &#160;http://sugo.io/?ad_source=google&ad_campaign=test&ad_media=vedio   
&#160; &#160; &#160; &#160;系统会为每个访问该URL的用户生成一个user_id。当打开广告页面后，上面有一个接待组件，每次访问和点击该按钮的操作会被上报。如果发起聊天，就会进入该广告主的客户库。每次访问都会产生一条记录，如果在访问中点击了接待组件，则会在相应的地方填上。产生的字段如下：

字段名| 描述
---|---
tid | id
timestamp | 访问时间
corpuin | 广告主id
host | 域名
device_type | 用户访问设备类型
is_new | 是否为新访客
ad_source | 广告来源
ad_media | 广告媒介
ad_capaign | 广告序列
user_id | 用户id
click_user_id | 如果用户点击了接待组件，则将user_id 填上
new_user_id | 如果用户是新用户，则将user_id  填上


## <a id="Timeseries" href="Timeseries"></a>  1. Timeseries


&#160; &#160; &#160; &#160;对于需要统计一段时间内的汇总数据，或者是指定时间粒度的汇总数据，Druid通过Timeseries来完成。

**example**  
&#160; &#160; &#160; &#160;对指定客户id和host，统计一段时间内的访问次数、访客数、新访客数、点击按钮数、新访客比例与点击按钮比例，可以用如下查询语句。

```
{
    "queryType": "lucene_timeseries",
    "dataSource": "visitor_statistics",
    "intervals":["2015-12-31T16:00:00+08:00/2017-04-14T15:59:59+08:00"],
    "granularity": "all",
    "filter":{
        "type":"and",
        "fields":[
            {
                "type":"selector",
                "dimension":"host",
                "value":"sugo.io"
            },
            {
                "type":"selector",
                "dimension":"corpuin",
                "value":2852199351
            }
        ]
    },
    "aggregations":[
        {
            "type":"longSum",
            "name":"pv",
            "fieldName":"count"
        },
        {
            "type":"hyperUnique",
            "name":"visitor_count",
            "fieldName":"visit_count"
        },
        {
            "type":"hyperUnique",
            "name":"new_visitor_count",
            "fieldName":"new_visit_count"
        },
        {
            "type":"hyperUnique",
            "name":"click_visitor_count",
            "fieldName":"click_visit_count"
        }
    ],
    "postAggregations":[
        {
            "type":"arthmetic",
            "name":"new_visitor_rate",
            "fn":"/",
            "fields":[
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"new_visitor_count"
                },
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"visitor_count"
                }
            ]
        },
        {
            "type":"arthmetic",
            "name":"click_rate",
            "fn":"/",
            "fields":[
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"click_visitor_count"
                },
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"visitor_count"
                }
            ]
        }
    ]
}
```

&#160; &#160; &#160; &#160;Timeseries查询包含如下部分。

字段名 | 描述 | 是否必须
---|---|---
queryType  | 对于Timeseries查询，该字段的值必须是lucene_timeseries | 是
dataSource | 要查询数据集dataSource名字。详见[`dataSource`](/developer/query/#dataSource) | 是
intervals  | 查询时间区间范围，ISO-8601 格式。详见[`interval`](/developer/query/#interval) | 是
granularity | 查询结果进行聚合的时间粒度 | 是
filter | 过滤条件。详见[`filter`](/developer/query/#filter) | 否
aggregations | 聚合。详见[`aggregation`](/developer/query/#aggregation) | 是
postAggregations | 后期聚合。详见[`post-aggregation`](/developer/query/#post-aggregation) | 否
descending | 是否降序 | 否
context | 指定一些查询参数，如结果是否进缓存等 | 否

&#160; &#160; &#160; &#160;Timeseries 输出每个时间粒度内指定条件的统计信息，通过filter 指定过滤条件，通过aggregations 和 postAggregations 指定聚合方式。

&#160; &#160; &#160; &#160;Timeseries不能输出维度信息，granularity支持all , none , second , minute , fifteen_minute , thirty_minute , hour , day , week , month , quarter , year。

- all，汇总为1条输出。
- none，不推荐使用。
- 其他的，则输出相应粒度的统计信息。

&#160; &#160; &#160; &#160;输出可能如下：
```
[
   {
        "timestamp":"2016-04-20T15:00:00.000Z",
        "result":{
            "pv":29.124896532178465,
            "visit_count"::5.003442568945213,
            "new_visitor_count": 0,
            "click_visitor_count":5.003442568945213,
            "new_visitor_rate": 0,
            "click_rate": 1
        }
   }
]
```
## <a id="TopN" href="TopN"></a> 2. TopN
&#160; &#160; &#160; &#160;TopN返回指定维度和 排序字段的有序top-n序列。TopN支持返回前N条记录，并支持指定Metric为排序依据。

**example**  
&#160; &#160; &#160; &#160;对指定广告主id=2852199100和指定host=sugo.io，以及来自PC或手机访问，希望获取访客数最高的3个ad_source，以及每个ad_source对应的访问次数、访客数、新访客数、点击按钮数、新访客比例、点击按钮比例、ad_campaign 与 ad_media 的组合个数，查询示例如下：
```
{
    "queryType": "lucene_topN",
    "dataSource": "visitor_statistics",
    "intervals":["2016-08-30T00:00:00+08:00/2016-09-05T23:59:59+08:00"],
    "granularity": "all",
    "dimension":"ad_source",
    "threshold":3,
    "metric":{
        "type":"numeric",
        "metric":"pv"
    },
    "filter":{
        "type":"and",
        "fields":[
            {
                "type":"selector",
                "dimension":"host",
                "value":"sugo.io"
            },
            {
                "type":"selector",
                "dimension":"corpuin",
                "value":2852199100
            }，
            {
                "type":"or",
                "fields":[
                    {
                        "type":"selector",
                        "dimension":"device_type",
                        "value":"1"
                    },
                    {
                        "type":"selector",
                        "dimension":"device_type",
                        "value":"2"
                    }
                ]
            }
        ]
    },
    "aggregations":[
        {
            "type":"longSum",
            "name":"pv",
            "fieldName":"count"
        },
        {
            "type":"hyperUnique",
            "name":"visitor_count",
            "fieldName":"visit_count"
        },
        {
            "type":"hyperUnique",
            "name":"new_visitor_count",
            "fieldName":"new_visit_count"
        },
        {
            "type":"hyperUnique",
            "name":"click_visitor_count",
            "fieldName":"click_visit_count"
        },
        {
            "type":"cardinality",
            "name":"sub_count",
            "fieldName":[
                "ad_campaign","ad_media"
            ],
            "byRow":true
        }
    ],
    "postAggregations":[
        {
            "type":"arthmetic",
            "name":"new_visitor_rate",
            "fn":"/",
            "fields":[
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"new_visitor_count"
                },
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"visitor_count"
                }
            ]
        },
        {
            "type":"arthmetic",
            "name":"click_rate",
            "fn":"/",
            "fields":[
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"click_visitor_count"
                },
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"visitor_count"
                }
            ]
        }
    ]
}
```
&#160; &#160; &#160; &#160;TopN查询包含以下部分：

字段名 | 描述 | 是否必须
---|---|---
queryType  | 对于TopN 查询，该字段的值必须是lucene_topN | 是
dataSource | 要查询数据集dataSource名字。详见[`dataSource`](/developer/query/#dataSource) | 是
intervals  | 查询时间区间范围，ISO-8601 格式。详见详见[`interval`](/developer/query/#interval) | 是
granularity | 查询结果进行聚合的时间粒度 | 是
filter | 过滤条件。详见[`filter`](/developer/query/#filter) | 否
aggregations | 聚合。详见[`aggregation`](/developer/query/#aggregation) | 是
postAggregations | 后聚合器。详见[`post-aggregation`](/developer/query/#post-aggregation)| 否
dimension | 进行TopN 查询的维度，一个TopN 查询指定且只能指定一个维度，如URL。详见[`dimension`](/developer/query/#dimension) | 是
threshold | TopN 的N 取值 | 是
metric | 进行统计并排序的Metric，如 PV | 是
context | 指定一些查询参数，如结果是否进缓存等 | 否

- metric: TopN专属，指定排序依据。它有如下使用方式：

```
"metric":"<metric_name>" //默认方式，升序排序
```

```
"metric":{
	"type":"numeric",   //指定按照numeric 降序排列 
	"metric":"<metric_name>"
}
```

```
"metric":{
	"type":"inverted",     //指定按照numeric 升序排列
	"metric":"<delegate_top_n_metric_spec>"
}
```

```
"metric":{
	"type":"lexicographic", //指定按照字典序排序
	"previousStop":"<previousStop_value>", //如b,按照字典序，排到b开头的为止
}
```
```
"metric":{
	"type":"alphaNumeric",  //指定数字排序
	"previousStop":"<previousStop_value>"
}
```
&#160; &#160; &#160; &#160;查询结果如下：
```
[
    {
        "timestamp":"2016-08-29T16:00:00.000Z",
        "result":[
            {
                "pv":5,
                "new_visitor_rate":1,
                "click_rate":0,
                "sub_count":1.0002442201269182,
                "new_visitor_count":7.011990219885757,
                "visitor_count":7.011990219885757,
                "ad_source":"baidu",
                "click_visitor_count":0
            },
            {
                "pv":4,
                "new_visitor_rate":1,
                "click_rate":0,
                "sub_count":1.0002442201269182,
                "new_visitor_count":4.003911343725148,
                "visitor_count":4.003911343725148,
                "ad_source":"google",
                "click_visitor_count":0
            },
            {
                "pv":3,
                "new_visitor_rate":1,
                "click_rate":0,
                "sub_count":1.0002442201269182,
                "new_visitor_count":4.003911343725148,
                "visitor_count":4.003911343725148,
                "ad_source":"sogou",
                "click_visitor_count":0
            }
        ]
    }
]
```
&#160; &#160; &#160; &#160;需要注意的是，topN是一个近似算法，每一个Segment返回前1000条进行合并得到最后的结果，如果dimension的基数在1000以内，则是准确的，超过1000就是近似值。

## <a id="GroupBy" href="GroupBy"></a> 3. GroupBy
&#160; &#160; &#160; &#160;GroupBy类似于SQL中的group by 操作，能对指定的多个维度进行分组，也支持对指定的维度进行排序，并输出limit行数。同时，支持having操作。GroupBy与TopN相比，可以指定更多的维度，但性能比TopN差很多。所以如果是对单个维度进行group by，则应尽量使用TopN。  
**example**  
&#160; &#160; &#160; &#160;查询每组ad_source、ad_campaign和ad_media对应的嗯访客数、新访客数、点击按钮数、新访客比例和点击按钮比例，查询示例如下：

```
{
    "queryType":"lucene_groupBy",   
    "dataSource": "visitor_statistics",
    "intervals":["2016-08-29T00:00:00+08:00/2016-09-04T23:59:59+08:00"],
    "granularity": "all",
    "dimension":[
        "ad_source",
        "ad_campaign",
        "ad_media"
    ],
    "limitSpec":{
        "type":"default",
        "limit":1000,
        "columns":[
            {
                "dimension":"visitor_count",
                "direction":"descending"
            }
        ]
    },
    "filter":{
        "type":"and",
        "fields":[
            {
                "type":"selector",
                "dimension":"host",
                "value":"sugo.io"
            },
            {
                "type":"selector",
                "dimension":"is_ad",
                "value":1
            }，
            {
                "type":"selector",
                "dimension":"corpuin",
                "value":"2852199351"
            }，
            {
                "type":"or",
                "fields":[
                    {
                        "type":"selector",
                        "dimension":"device_type",
                        "value":"1"
                    },
                    {
                        "type":"selector",
                        "dimension":"device_type",
                        "value":"2"
                    }
                ]
            }
        ]
    },
    "aggregations":[
        {
            "type":"hyperUnique",
            "name":"visitor_count",
            "fieldName":"visit_count"
        },
        {
            "type":"hyperUnique",
            "name":"new_visitor_count",
            "fieldName":"new_visit_count"
        },
        {
            "type":"hyperUnique",
            "name":"click_visitor_count",
            "fieldName":"click_count"
        },
    ],
    "postAggregations":[
        {
            "type":"arthmetic",
            "name":"click_rate",
            "fn":"/",
            "fields":[
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"click_visitor_count"
                },
                {
                    "type":"hyperUniqueCardinality",
                    "filedName":"visitor_count"
                }
            ]
        }
    ]
}
```
&#160; &#160; &#160; &#160;GroupBy 查询包含以下部分：

字段名 | 描述 | 是否必须
---|---|---
queryType  | 对于GroupBy 查询，该字段的值必须是lucene_groupBy | 是
dataSource | 要查询数据集dataSource名字。详见[`dataSource`](/developer/query/#dataSource) | 是
dimensions | 进行GroupBy 查询的维度集合。详见[`dimension`](/developer/query/#dimension) | 是
limitSpec  | 对统计结果进行排序，取limit的行数 | 否
having     | 对统计结果进行筛选。详见[`having`](/developer/query/#having) | 否
granularity | 查询结果进行聚合的时间粒度 | 是
filter | 过滤条件。详见[`filter`](/developer/query/#filter) | 否
aggregations | 聚合。详见[`aggregation`](/developer/query/#aggregation) | 是
postAggregations | 后聚合器。详见[`post-aggregation`](/developer/query/#post-aggregation) | 否
intervals  | 查询时间区间范围，ISO-8601 格式。详见[`interval`](/developer/query/#interval) | 是
context    | 指定一些查询参数，如结果是否进缓存等 | 否

&#160; &#160; &#160; &#160;GroupBy 特有的字段为limitSpec和having。

- **limitSpec**  

&#160; &#160; &#160; &#160;指定排序规则和limit的行数。JSON 示例如下：
```
{
    "type":"default",
    "limit":<integer_value>,
    "columns":[list of OrderByColumnSpec]
}
```
&#160; &#160; &#160; &#160;其中columns是一个数组，可以指定多个排序字段，排序字段可以使demension或metric，指定排序规则的拼写方式：
```
{
    "dimension":"<Any dimension or metric name>",
    "direction":<"ascending"|"descending">
}
```
&#160; &#160; &#160; &#160;示例如下：
```
"limitSpec":{
    "type":"default",
    "limit":1000,
    "columns":[
        {
            "dimension":"visitor_count",
            "direction":"descending"
        },
        {
            "dimension":"click_visitor_count",
            "direction":"ascending"
        }
    ]
}
```

- **having**

&#160; &#160; &#160; &#160; 类似于SQL中的having操作，对GroupBy的结果进行筛选，详见[`having`](/developer/query/#having)。


## <a id="Select" href="Select"></a> 4. Select

&#160; &#160; &#160; &#160;Select类似于SQL中的select操作，Select用来查看Druid中存储的数据，并支持按照指定过滤器和时间段查看指定维度和Metric。能通过descending字段指定排序顺序，并支持分页拉取，但不支持aggregations和postAggregations。

JSON示例如下：
```
{
    "queryType":"lucene_select",
    "dataSource": "visitor_statistics",
    "descending":false,
    "dimensions":[
        "tid",
        "corpuin",
        "host",
        "device_type",
        "is_new",
        "ad_source",
        "ad_media",
        "ad_campaign"
    ],
    "intervals":["2016-08-29/2016-08-31"]，
    "metrics":[
        "count",
        "visit_count",
        "new_visit_count",
        "click_visit_count"
    ],
    "pagingSpec":{
        "pagingIdentifiers":{},
        "threshold":5
    },
    "queryType":"select"
}
```
&#160; &#160; &#160; &#160;在pagingSpec中指定分页拉取的offset和条目数，在结果中会返回下次拉取的offset。JSON示例如下：
```
{
    "pagingSpec":{
        "pagingIdentifiers":{},
        "thershold":5,
        "fromNext":true
    }
}
```

## <a id="Search" href="Search"></a> 5. Search
&#160; &#160; &#160; &#160;Search查询返回匹配中的维度，类似于SQL中的like操作，但是支持更多的匹配操作。JSON示例如下：
```
{
    "queryType":"lucene_search",
    "dataSource":"sample_datasource", 
    "granularity":"day", 
    "intervals":["2013-01-01T00:00:00.000Z/2013-01-03T00:00:00.000Z"],
    "limit":50, 
    "searchDimensions":[
        "dim1",
        "dim2"
    ],
    "sort":{
        "type":"lexicographic"
    }
}
```
- searchDimensions:搜索的维度

&#160; &#160; &#160; &#160;需要注意的是，Search只是返回匹配中维度，不支持其他聚合操作。如果要将Search作为查询条件进行TopN、GroupBy或Timeseries等操作，则可以在filter字段中指定各种过滤方式。filter字段也支持正则匹配。

## 6. 元数据查询
&#160; &#160; &#160; &#160;Druid支持对DataSource的基础元数据进行查询。

### <a id="TimeBoundary" href="TimeBoundary"></a> 6.1 TimeBoundary
&#160; &#160; &#160; &#160;通过TimeBoundary可查询DataSource的最早和最晚的时间点，查询JSON示例如下：
```
{
    "queryType":"lucene_timeBoundary",
    "dataSource": "sample_datasource",
    "bound":<"maxTime" | "minTime">
}
```
- bound：最小最大时间，maxTime or minTime

&#160; &#160; &#160; &#160;返回结果如下：
```
[
    {
        "result":{
            "maxTime":"2013-05-09T18:37:00.000Z",
            "minTime":"2013-05-09T18:24:00.000Z"
        },
        "timeStamp":"2013-05-09T18:24:00.000Z"
    }
]
```

### <a id="SegmentMetadata" href="SegmentMetadata"></a> 6.2 SegmentMetadata
&#160; &#160; &#160; &#160;通过SegmentMetadata 可查询Segment 的元信息，如有哪些column、metric、aggregator，查询JSON示例如下：
```
{
    "queryType":"lucene_segmentMetadata",
    "dataSource": "sample_datasource",
    "intervals": ["2013-01-01/2014-01-01"]
}
```
&#160; &#160; &#160; &#160;返回结果如下：
```
[
    {
        "aggregators":{
            "metric1":{
                "fieldName":"metric1",
                "name":"metric1",
                "type":"longSum"
            }
        },
        "columns":{
            "__time":{
                "cardinality":null,
                "errorMessage":null,
                "hasMultipleValues":false,
                "size":407240380,
                "type":"LONG"
            },
            "dim1":{
                "cardinality":1944,
                "errorMessage":null,
                "hasMultipleValues":false,
                "size":100000,
                "type":"STRING"
            },
            "dim2":{
                "cardinality":1504,
                "errorMessage":null,
                "hasMultipleValues":true,
                "size":100000,
                "type":"STRING"
            },
            "metric1":{
                "cardinality":null,
                "errorMessage":null,
                "hasMultipleValues":false,
                "size":100000,
                "type":"FLOAT"
            }
        },
        "id":"some_id",
        "intervals":[2013-05-13T00:00:00.000Z/2013-05-14T00:00:00.000Z],
        "numRows":5000000,
        "queryGranularity":{
            "type":"none"
        },
        "size":300000
    }
]
```
&#160; &#160; &#160; &#160;segmentMetadata 支持更多的查询字段，不过这些字段都不是必须的，具体如下：

字段名 | 描述 | 是否必须
---|--- |---
toInclude | 可以指定哪些column在返回结果中呈现，可以填all,none,list | 否
merge | 将多个Segment 的元信息合并到一个返回结果中 | 否
analysisTypes | 指定返回column 的哪些属性，如 size,intervals 等 | 是
lenientAggregatorMerge | true或false，设置为团社时，讲不通的aggregator合并显示 | 否
context | 查询Context，可以指定是否缓存查询结果等 | 否

- toInclude的使用方式如下：
```
"toInclude":{"type":"all"}
"toInclude":{"type":"none"}
"toInclude":{"type":"list","columns":[<string list of column names>]}
```
- analysisTypes支持指定的属性：cardinality,minmax,size,intervals,queryGranularity,aggregators。