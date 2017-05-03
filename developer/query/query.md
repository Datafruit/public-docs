# 查询

## groupBy
```
{
	"queryType":"lucene_groupBy",   
	"dataSource":"com_HyoaKhQMl_project_rJIXzFOpe",
	"intervals":[
			 "2015-12-31T16:00:00.000Z/2017-04-14T15:59:59.999Z"
	],
	"filter":{
		"type":"and",	
		"fields":[
			<filter>,<filter>,...
		]
	}, 
	"granularity": "all", 
	"dimension": ["name"],
	"aggregations": [
	    {
	    	"type": "lucene_filtered",
	      	"aggregator": {
		        "type": "lucene_thetaSketch",
		        "name": "userTotal",
		        "fieldName": "UserID"  
	      	},
	      	"filter": {
		        "type": "bound",
		        "dimension": "__time",
		        "lower": "1451577600000",
		        "upper": "1492185599999",
		        "lowerStrict": true,
		        "upperStrict": true
	      	}
	    },
	    {
	      	"type": "lucene_filtered",
	      	"aggregator": {
		        "type": "lucene_thetaSketch",
		        "name": "prevUserTotal",
		        "fieldName": "UserID"
	      	},
	      	"filter": {
		        "type": "bound",
		        "dimension": "__time",
		        "lower": "1451577600000",
		        "upper": "1492099199999",
		        "lowerStrict": true,
		        "upperStrict": true
	      	}
	    }
  	],
	"postAggregations":[
		<postAggregator>,<postAggregator>,...
	],
	"having":{
		"type":"and",
		"havingSpecs":[
			<havingSpec>,<havingSpec>,...
		]
	},
	"limitSpec":{
		"type":"default",
		"columns":[
			{
				"dimension":"nation",
				"direction":"ASCENDING", 
				"dimensionOrder":"lexicographic"
			}
		],
		"limit":50
	},
	"context":{
		"key": "value"
	}
}
```
- queryType: 查询的类型，区分不同的查询  
- dataSource: 数据源的名称，类似关系数据库中的表名  
- intervals: 查询的时间段  
- filter: 过滤条件  
- filter.type: 过滤类型  
- filter.fields: and过滤条件的参数  
- granularity:查询粒度  
    all： 将所有内容都装入一个bucke中。  
    none： 没有数据桶（它实际上使用了索引的粒度最小在none这是毫秒的粒度）。目前不推荐none在TimeseriesQuery中使用（系统将尝试生成不存在的所有毫秒的0值，这通常是很多的）。
- dimension: 分组的维度  
- aggregations: 聚合函数  
- postAggregations: 基于聚合函数的结果进一步处理  
- having: having条件  
- having.type: having的类型  
- having.havingSpecs: and类型的Having的参数  
- limitSpec.type: limit的类型    
- limitSpec.limit: 数量限制  
- limitSpec.columns: 排序的维度  
- limitSpec.columns.dimension:排序的维度  
- limitSpec.columns.direction:正序或倒序，ASCENDING or DESCENDING;  
- limitSpec.columns.dimensionOrder:排序的方式，lexicographic、alphanumeric、numeric、strlen  
- context: 其他context参数

## search
queryType=lucene_search 时，参数：
```
{
	"queryType":"lucene_search",
	"dataSource": "com_HyoaKhQMl_project_rJIXzFOpe", 
	"filter":{
		"type":"and",	
		"fields":[
			<filter>,<filter>,...
		]
	},
	"granularity":"all", 
	"limit":50, 
	"intervals": [
    	"2015-12-31T16:00:00.000Z/2017-04-14T15:59:59.999Z"
  	],
	"searchDimensions":[
		"name"
	],
	"sort":{
		"type":"lexicographic"
	},
	"context":{
		"key": "value"
	}
}
```
- searchDimensions:搜索的维度

## segmentMetadata
queryType=lucene_segmentMetadata 时，参数：
```
{
	"queryType":"lucene_segmentMetadata",
	"dataSource": "com_HyoaKhQMl_project_rJIXzFOpe",
	"intervals": [
    	"2015-12-31T16:00:00.000Z/2017-04-14T15:59:59.999Z"
  	],
	"toInclude":{
		"type":"none"
	},
	"merge":true,
	"context":{
		//Map<String, Object>
	},  
	"analysisTypes":[
		//EnumSet<AnalysisType> analysisTypes
	],
	"usingDefaultInterval":true,
	"lenientAggregatorMerge":true
}
```
- toInclude.type: none,不包含  
- toInclude.type: all,包含所有,默认选项  
- toInclude.type: list，包含指定项，此时需要toInclude.columns参数，值为["name", "age"]     
- merge:是否合并所有元数据，默认为false  
- analysisTypes:默认不填写，可以使用如下选项：  
    CARDINALITY,    
    SIZE,  
    INTERVAL,  
    AGGREGATORS,  
    MINMAX,  
    QUERYGRANULARITY;

## select
queryType=lucene_select 时，参数：
```
{
    "queryType":"queryType=lucene_select",
    "dataSource": "wuxianjiRT",
    "intervals": "2017-02-10T02:00:00.000Z/2017-02-10T10:00:00.000Z",
    "descending":true,
    "filter":{
		"type":"and",	
		"fields":[
			<filter>,<filter>,...
		]
    },
    "granularity": "all",
    "dimensions": [
	    {
		    "type": "default",
		    "dimension": "ClientDeviceID",
		    "outputName": "ClientDeviceId"      
	    }
    ],
    "pagingSpec": {
	    "pagingIdentifiers": {
		    "wuxianjiRT_2017-02-10T02:00:00.000Z_2017-02-10T10:00:00.000Z_2017-02-10T00:00:00.896Z_3": -13,
		    "wuxianjiRT_2017-02-10T02:00:00.000Z_2017-02-10T10:00:00.000Z_2017-02-10T00:00:00.896Z_2": -13,
		    "wuxianjiRT_2017-02-10T02:00:00.000Z_2017-02-10T10:00:00.000Z_2017-02-10T00:00:00.896Z_1": -12,
		    "wuxianjiRT_2017-02-10T02:00:00.000Z_2017-02-10T10:00:00.000Z_2017-02-10T00:00:00.896Z": -12
	    },
    	"threshold": 50,
    	"fromNext":true
    },
    "context": {
	    "timeout": 600000
    }
}
```
- pagingSpec:分页信息  
- pagingSpec.pagingIdentifiers:段信息，一般第一次查询时不填写，需要查第n页时，从上一次查询中获取该信息。  
- pagingSpec.threshold：分页大小  
- pagingSpec.fromNext：下一页从下一条记录开始，需要设置为true  
- context.timeout:查询超时时间

## timeBoundary
queryType=lucene_timeBoundary 时，参数：
```
{
    "dataSource": "com_HyoaKhQMl_project_rJIXzFOpe",
    "intervals": [
    	"2015-12-31T16:00:00.000Z/2017-04-14T15:59:59.999Z"
    ],
    "bound": "bound_string", 
    "context": {
	    "timeout": 600000
    }
}
```
- bound：最小最大时间，maxTime or minTime

## timeseries
queryType=lucene_timeseries 时，参数：
```
{
  	"queryType": "lucene_timeseries",
  	"dataSource": "com_HyoaKhQMl_project_rJIXzFOpe",
  	"intervals": [
    	"2015-12-31T16:00:00.000Z/2017-04-14T15:59:59.999Z"
  	],
  	"granularity": "all",
 	"context": {
   		"timeout": 180000
 	},
  	"aggregations": [
	    {
	    	"type": "lucene_filtered",
	      	"aggregator": {
		        "type": "lucene_thetaSketch",
		        "name": "userTotal",
		        "fieldName": "UserID"  
	      	},
	      	"filter": {
		        "type": "bound",
		        "dimension": "__time",
		        "lower": "1451577600000",
		        "upper": "1492185599999",
		        "lowerStrict": true,
		        "upperStrict": true
	      	}
	    },
	    {
	      	"type": "lucene_filtered",
	      	"aggregator": {
		        "type": "lucene_thetaSketch",
		        "name": "prevUserTotal",
		        "fieldName": "UserID"
	      	},
	      	"filter": {
		        "type": "bound",
		        "dimension": "__time",
		        "lower": "1451577600000",
		        "upper": "1492099199999",
		        "lowerStrict": true,
		        "upperStrict": true
	      	}
	    }
        ],
        "postAggregations": [
    	    {
    	      	"type": "lucene_thetaSketchEstimate",
    	     	"name": "userTotal1",
    	      	"field": {
    	        "type": "fieldAccess",
    	        "fieldName": "userTotal"
    	    	},
          		"trunc": true
        	},
           	{
    	      	"type": "lucene_thetaSketchEstimate",
    	      	"name": "prevUserTotal1",
    	      	"field": {
    	        "type": "fieldAccess",
    	        "fieldName": "prevUserTotal"
          		},
          	"trunc": true
        	}
        ]   
}
```

## topN
TopN查询根据某些条件返回给定维度中的值的排序集合。  

queryType=lucene_topN 时，参数：
```
{
    "dataSource": "com_HyoaKhQMl_project_rJIXzFOpe",
    "dimension": "__time",
    "metric":{
    	"type":"numeric",
    	"numeric":"<numericString>"
    },
    "threshold": 100,
    "intervals": [
    	"2015-12-31T16:00:00.000Z/2017-04-14T15:59:59.999Z"
    ],
    "filter":{
	"type":"and",	
	"fields":[
		<filter>,<filter>,...
	]
    },  
    "granularity": "all",
    "aggregations": [
	    {
	    	"type": "lucene_filtered",
	      	"aggregator": {
		        "type": "lucene_thetaSketch",
		        "name": "userTotal",
		        "fieldName": "UserID"  
	      	},
	      	"filter": {
		        "type": "bound",
		        "dimension": "__time",
		        "lower": "1451577600000",
		        "upper": "1492185599999",
		        "lowerStrict": true,
		        "upperStrict": true
	      	}
	    },
	    {
	      	"type": "lucene_filtered",
	      	"aggregator": {
		        "type": "lucene_thetaSketch",
		        "name": "prevUserTotal",
		        "fieldName": "UserID"
	      	},
	      	"filter": {
		        "type": "bound",
		        "dimension": "__time",
		        "lower": "1451577600000",
		        "upper": "1492099199999",
		        "lowerStrict": true,
		        "upperStrict": true
	      	}
	    }
    ],
    "postAggregations": [
	{
	      	"type": "lucene_thetaSketchEstimate",
	     	"name": "userTotal1",
	      	"field": {
	        "type": "fieldAccess",
	        "fieldName": "userTotal"
	    	},
      		"trunc": true
    	},
       	{
	      	"type": "lucene_thetaSketchEstimate",
	      	"name": "prevUserTotal1",
	      	"field": {
	        "type": "fieldAccess",
	        "fieldName": "prevUserTotal"
      		},
      	        "trunc": true
    	}
    ],
    "context": {
	    "timeout": 600000,
	    "useOffheap": true
    }
}
```
- threshold:topN的记录数  

metric.type可选项： numeric , lexicographic , alphaNumeric , inverted , 也可以是一个对象

metric.type=lexicographic 时，参数：
```
{
	"type":"lexicographic",
	"previousStop":"<previousStop_string>"
}
```
metric.type=alphaNumeric 时，参数：
```
{
	"type":"alphaNumeric",
	"previousStop":"<previousStop_string>"
}
```
metric.type=inverted 时，参数：
```
{
	"type":"inverted",
	"metric":{
		<topNMetricSpec>
	}
}
```
