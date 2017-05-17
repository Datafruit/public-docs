# Tindex 查询接口使用文档

Tindex的原生查询接口是HTTP REST风格查询方式，还有其它客户库查询方式比如：[sugo-plyql](developer/interfaces/sugo-plyql.md)  

使用HTTP REST风格查询（`Broker`, `Historical`, 或者 `Realtime`）节点数据。

查询参数为JSON格式，每个节点类型都会暴露相同的REST查询接口。

对于正常的Tindex操作，应该向`Broker`节点发出查询。
查询请求示例如下：

```shell
 curl -X POST '<queryable_host>:<port>/druid/v2/?pretty' -H 'Content-Type:application/json' -d @<query_json_file>
```

 `queryable_host: ` Broker节点ip:8082 (`http://192.168.0.200:8082`)  

 `query_json_file: ` 查询参数详见[Tindex-Query-Json](/developer/query/query.md)部分

- [Tindex-Query-Json](/developer/query/query.md) 属性详情如下：
  - [`dataSource`](#dataSource)
  - [`dimension`](#dimension)
  - [`interval`](#interval)
  - [`filter`](#filter)
  - [`extraction-fn`](#extraction-fn)
  - [`aggregation`](#aggregation)
  - [`post-aggregation`](post-aggregation)
  - [`having`](#having)

## <a id="dataSource" href="dataSource"></a> dataSource 数据源

数据源相当于数据库中的表

dataSource.type可选项： table , query , union , 也可以是一个字符串()。

###  表数据源
dataSource.type=table 时，参数：    
```
{
    "type":"table",  
    "name":"<string_value>"
}
```
最常用的数据源，`<string_value>`为源数据源的名称，类似关系数据库中的表名。

### 联合数据源
dataSource.type=union时，参数：
```
{
    "type": "union",
    "dataSources": ["<string_value1>", "<string_value2>", "<string_value3>", ... ]
}
```
该数据源连接两个或多个表数据，`<string_value1>` `<string_value2>` `<string_value3>` 为表数据源的名称。

### 查询数据源
dataSource.type=query时，参数：
```
{
    "type":"query",
    "query":{
		//Query
    }   
}
```
可以进行查询的嵌套。


## <a id="dimension" href="dimension"></a> dimension 维度
可以在查询中使用以下JSON字段来操作维度值。  
dimensions.type 可选项： `default`, `extraction` , `regexFiltered` , `listFiltered` , `lookup`，也可以是一个对象  

### 默认
dimensions.type=default 时，参数：
```javascript
{
    "type":"default",
    "dimension":"<dimension>",
    "outputName":"<output_name>"
}
```
返回维度值，并可选择对维度进行重命名。

### 提取
dimensions.type=extraction 时，参数：
```javascript
{
    "type":"extraction",
    "dimension":"<dimension>",
    "outputName":"<output_name>",
    "extraction":{
    	<extraction_function>
    },
    "dimExtractionFn":{     
    	<extraction_function>
    }
}
```
返回使用给定提取函数转换的维度值。

### 正则表达式
dimensions.type=regexFiltered 时，参数：
```javascript
{
    "type":"regex",
    "delegate":{
      	<dimensionSpec>
    },
    "pattern":"pattern_string"
}
```
返回给定正则表达式的第一个匹配组。如果没有匹配，则返回维度值。  
例如，使用`"expr" : "(\\w\\w\\w).*"`将改变'Monday'，'Tuesday'，'Wednesday'成'Mon'，'Tue'，'Wed'。

### 过滤
dimensions.type=listFiltered 时，参数：
```javascript
{
    "type":"listFiltered",
    "delegate":{
      	 <dimensionSpec>
    },
    "values":[
    	"<value_string>","<value_string>",...
    ],
    "isWhitelist":true
}
```
仅适用于多值维度。如果您在druid中有一行具有值为`[“v1”，“v2”，“v3”]`的多值维度，并且通过该维度使用查询过滤器为值“v1” 发送groupBy / topN查询分组。在响应中，您将获得包含“v1”，“v2”和“v3”的3行。对于某些用例，此行为可能不直观。

### 查找
dimensions.type=lookup 时，参数：
```javascript
{
    "type":"lookup",
    "dimension":"<dimensionName>",
    "outputName":"<dimensionOutputName>",
    "lookup":{
    	"type":"map",
    	"map":{
    		"key1":"value1",
    		"key2":"value2"
    	},
    	"isOneToOne":false 	
    },
    "retainMissingValue":false,
    "replaceMissingValueWith":"<missing_value>",
    "name":"<name_string>",
    "optimize":true
}
```
可用于将查找实现直接定义为维度规范。  

在查询时可以指定属性retainMissingValue为false，并通过设置replaceMissingValueWith提示如何处理缺失值。  
retainMissingValue如果在查找中找不到，设置为true将使用维度的原始值。
默认值是replaceMissingValueWith = null，retainMissingValue = false并且导致丢失的值被视为丢失值。


## <a id="interval" href="interval"></a> interval 时间区间

intervals.type可选项： intervals , segments , 也可以是一个字符串，比如"2015-12-31T16:00:00.000Z/2017-04-14T15:59:59.999Z"

### intervals
intervals.type=intervals时，参数：
```
{
	"type":"intervals",
	"intervals":[
    	    <interval>,<interval>,...
	]
}
```
- intervals:可以定义多个区间


### 段
intervals.type=segments时，参数：
```
{
    "type":"segments",
    "segments":[
    	{
        	"itvl":{
			<interval>
		},
		"ver":"<version>",
		"part":100
	},
	{
		"itvl":{
			<interval>
		},
		"ver":"<version>",
		"part":200
	},...
    ]
}
```
可定义多个段


# Tindex-Query-Json `filter`属性详情如下

## <a id="filter" href="filter"></a> filter 过滤器
一个过滤器是一个JSON对象，指示在查询的计算中应该包括哪些数据行。它基本上等同于SQL中的WHERE子句。  

filter.type可选项: all ，and , not , or , in , lookup , lucene , selector , regex , search , javascript ,  bound

### 选择过滤器

选择器过滤器将与具体值匹配,可用作更复杂过滤器的基本过滤器。  

上面的参数设置这相当于 `WHERE <dimension_string> = '<value_string>'`。

支持使用提取功能。

filter.type=selector 时，参数：
```
{
    "type":"selector",
    "dimension":"dimension_string",
    "value":"value_string",
    "extractionFn": {
    	"type":"time",
    	"timeFormat":"timeformat_string",
    	"resultFormat":"resultformat_string"
    }
}
```


### 正则表达式过滤器

正则表达式过滤器与选择器过滤器类似，但使用正则表达式。它与给定模式匹配指定的维度。  

pattern：给定的模式，可以是任何标准的Java正则表达式。    

支持使用提取功能。  

filter.type=regex 时，参数：
```
{
    "type":"regex",
    "dimension":"dimension_string",
    "pattern":"pattern_string",
    "extractionFn":{
    	<extractionFn>
    }
}
```

### 逻辑表达式过滤器

#### 和过滤器
filter.type=and 时，参数：
```
{
    "type":"and",
    "fields":[
	<filter>, <filter>, ...
    ]
}
```
`<filter>`可以是任何一种过滤器。

#### 或过滤器
filter.type=or 时，参数：
```
{
    "type":"or",
    "fields":[
	<filter>, <filter>, ...
    ]
}
```
`<filter>`可以是任何一种过滤器。

#### 非过滤器
filter.type=not 时，参数：
```
{
    "type":"not",
    "fields":<filter>
}
```
`<filter>`可以是任何一种过滤器。

#### JavaScript过滤器

JavaScript过滤器将维度与指定的JavaScript谓语函数进行匹配。  

支持使用提取功能。

filter.type=javascript 时，参数：
```
{
    "type":"javascript",
    "dimension":"<dimension_string>",
    "function":"<function_string>",
    "extractionFn":{
    	<extractionFn>
    }
}
```
**example**
```
{
  "type":"javascript",
  "dimension":"name",
  "function":"function(x) { return(x >= 'bar' && x <= 'foo') }"
}
```
上面的例子可匹配任何name在'bar'和'foo'之间的维度值。


### 搜索过滤器

搜索过滤器可用于过滤部分字符串匹配。

filter.type=search 时，参数：
```
{
    "type":"search",
    "dimension":"dimension_string",
    "query":{
    	"type":"contains",
      	"value":"value_string",
        "caseSensitive":true
    },
    "extractionFn":{
    	<extractionFn>
    }
}
```

query.type:搜索类型，contains、insensitive_contains、fragment、regex  

query.type=contains 时，参数：
```
{
    "type":"contains",
    "value":"value_string",
    "caseSensitive":true
}
```
caseSensitive：是否大小写敏感

query.type=insensitive_contains 时，参数：
```
{
    "type":"insensitive_contains",
    "value":"value_string"
}
```
query.type=fragment 时，参数：
```
{
    "type":"fragment",
    "values":["<value_string>","<value_string>",...],
    "caseSensitive":true
}
```
query.type=regex 时，参数：
```
{
    "type":"regex",
    "pattern":"pattern_string"
}
```

### 边界过滤器

边界过滤器可用于过滤维度值的范围。它可以用于大于，小于，大于或等于，小于或等于和“之间”（如果同时设置“下”和“上”两者）的比较过滤。  

支持提取功能  

filter.type=bound 时，参数：
```
{
    "type":"bound",
    "dimension":"dimension_string",
    "lower":"0",
    "upper":"100",
    "lowerStrict":false,
    "upperStrict":false,
    "alphaNumeric":true,
    "extractionFn":{
    	<extractionFn>
    }
}
```
lowerStrict：是否包含下界  
upperStrict：是否包含上界


### in过滤器

filter.type=in 时，参数：
```
{
    "type":"in",
    "dimension":"dimension_string",
    "values":[
    	<value_string>,<value_string>,...
    ],
    "extractionFn":{
    	//ExtractionFn
    }
}
```

### all过滤器

匹配所有维度值

filter.type=all 时，参数:
```
{
    "type":"all"
}
```

### lookup过滤器

filter.type=lookup 时，参数
```
{
    "type":"all",
    "dimension":"<dimension_string>",
    "lookup":"<lookup_string>"
}
```      
      

### lucene过滤器

filter.type=lucene 时，参数
```
{
    "type":"all",
    "query":"<query_string>"
}
```


## <a id="extraction-fn" href="extraction-fn"></a> extraction-fn 提取过滤器

提取过滤器使用一些特定的提取function匹配维度。  

extraction.type可选项：time , regex , partial , searchQuery , javascript , timeFormat , identity , lookup , registeredLookup , substring , cascade , stringFormat , upper , lower 

### 时间
extraction.type=time 时，参数：
```
{
    "type":"time",
    "timeFormat": "<timeFormat_string>",
    "resultFormat": "<resultFormat_string>",
}
```
将日期格式提取为指定的格式

### 正则表达式
extraction.type=regex 时，参数：
```
{
    "type":"regex",
    "expr": "expr_string",
    "replaceMissingValue": true,
    "replaceMissingValueWith":"replace_string"
}
```
对匹配正则表达式的维度值进行提取
- expr:表达式
- replaceMissingValue:是否替换缺失的值
- replaceMissingValueWith：以什么字符串进行替换


### 分部
filter.extraction.type=partial 时，参数：
```
{
    "type":"partial",
    "expr": "expr_string"
}
```


### 搜索查询
filter.extraction.type=searchQuery 时，参数：
```
{
	"type":"searchQuery",
	"query":{
    	    "type":"contains",
	    "value":"value_string",
	    "caseSensitive":true
	}
}
```
- caseSensitive:是否大小写敏感



### javascript
filter.extraction.type=javascript 时，参数：
```
{
    "type":"javascript",
    "query":{
	"type":"contains",
	"function":"function_string",
      	"injective":true
    }
}
```
- function:javascript函数  

按照javascript的函数进行提取

### 时间格式

filter.extraction.type=timeFormat 时，参数：
```
{
    "type":"timeFormat",
    "query":{
	"type":"contains",
	"format":"pattern_string",
        "timeZone":{
    	    <dateTimeZone>
    	},
      	"locale":"locale_string"
    }
}
```
以特定格式，时区或语言环境来提取时间戳。

- timeZone:时区
- locale:地点

**example**

```
"filter": {
  "type": "selector",
  "dimension": "__time",
  "value": "Friday",
  "extractionFn": {
    "type": "timeFormat",
    "format": "EEEE",
    "timeZone": "America/New_York",
    "locale": "en"
  }
}
```

### 自增
filter.extraction.type=identity 时，参数：
```
{
    "type":"identity"
}
```
提取identity

### 查找
filter.extraction.type=lookup 时，参数：
```
{
    "type":"lookup",
    "lookup": {
	    "lookup":<lookup>, 
	    "retainMissingValue":true 
	    "replaceMissingValueWith":"<replaceMissingValueWith_string>", 
	    "injective":true, 
	    "optimize":true
    },    
    "retainMissingValue":true,	
    "replaceMissingValueWith":"replace_string",	
    "injective":true, 
    "optimize":true
}
```
**example**
```
{
    "filter": {
        "type": "selector",
        "dimension": "product",
        "value": "bar_1",
        "extractionFn": {
            "type": "lookup",
            "lookup": {
                "type": "map",
                "map": {
                    "product_1": "bar_1",
                    "product_5": "bar_1",
                    "product_3": "bar_1"
                }
            }
        }
    }
}
```

### registeredLookUp

filter.extraction.type=registeredLookup 时，参数：
```
{
    "type":"registeredLookup",
    "lookup":"lookup_string",
    "retainMissingValue":true,   
    "replaceMissingValueWith":"replace_string",  
    "injective":true,  
    "optimize":true, 
}
```


### 截取字符串
filter.extraction.type=substring 时，参数：
```
{
    "type":"substring",
    "index":10,
    "length":20
}
```
- index:起始位置
- length:截取的长度  

将字符串按照指定的起始位置和长度进行截取

### 级联
filter.extraction.type=cascade 时，参数：
```
{
    "type":"cascade",
    "extractionFns":[
    	    <extraction>,<extraction>,...
    ]
}
```

### 字符串格式
filter.extraction.type=stringFormat 时，参数：
```
{
    "type":"stringFormat",
    "format":"format_string",
    "nullHandling":{
 	    <nullHandling>
    }  
}
```
- format:格式
将字符串按照指定的格式进行提取

### 大写
filter.extraction.type=upper 时，参数：
```
{
    "type":"upper",
    "locale":"locale_string"
}
```
将指定的字符串提取成小写的格式。

### 小写
filter.extraction.type=lower 时，参数：
```
{
    "type":"lower",
    "locale":"locale_string"
}
```
将指定的字符串提取为大写的格式。


## <a id="aggregation" href="aggregation"></a> aggregation 聚合

聚合函数是查询规范的一部分，可以在数据进入druid之前对其进行总结处理。

aggregations.type 可选项：`lucene_cardinality` ， `lucene_count` ，`lucene_doubleMax` ，`lucene_doubleMin` ，`lucene_doubleSum` ， `lucene_hyperUnique` ， `lucene_javascript` ， `lucene_longMax` ， `lucene_longMin` , `lucene_longSum`

### 基数
aggregations.type=lucene_cardinality 时，参数：
```
{
    "type":"lucene_cardinality",
    "name":"name_string",
    "fieldNames":[
    	"<fieldName_string>","<fieldName_string>",...
    ], 
    "byRow":true 
}
```
计算一组druid维度的基数,相当于distinct()。  

当设置byRow为false（默认值）时，它计算由所有给定维度的所有维度值的并集组成  
的集合的基数。

### 计数
aggregations.type=lucene_count 时，参数：
```
{
    "type":"lucene_count",
    "name":"<name_string>",
}
```
可以计算行的数量，相当于`count()`

### 最大值（double）
aggregations.type=lucene_doubleMax 时，参数：
```
{
    "type":"lucene_doubleMax",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
}
```
求查询到的值中的最大值，该值类型为double，相当于`max("<fieldName_string>")`

### 最小值（double）
aggregations.type=lucene_doubleMin 时，参数：
```
{
    "type":"lucene_doubleMin",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>" 
}
```
求查询到的值中的最小值，该值类型为double，相当于`max("<fieldName_string>")`

### 总和（double）
aggregations.type=lucene_doubleSum 时，参数：
```
{
    "type":"lucene_doubleSum",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>" 
}
```
将查询到的值的和计算为double类型的数，相当于`sum("<fieldName_string>")`

### hyperUnique
aggregations.type=lucene_hyperUnique 时，参数：
```
{
    "type":"lucene_hyperUnique",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
}
```

### javascript

aggregations.type=lucene_javascript 时，参数：
```
{
    "type":"lucene_javascript",
    "name":"<name_string>",
    "fieldNames":[
    	"<fieldName_string>","<fieldName_string>"
    ], 
    "fnAggregate":"<fnAggregate_string>", 
    "fnReset":"<fnReset_string>", 
    "fnCombine":"<fnCombine_string>" 
}
```
计算一组任意JavaScript函数（允许使用度量和维度）。 
- name:这组JavaScript函数的名称
- fieldNames:参数的名字  

 
**example**
```
{
  "type": "lucene_javascript",
  "name": "sum(log(x)*y) + 10",
  "fieldNames": ["x", "y"],
  "fnAggregate" : "function(current, a, b)      { return current + (Math.log(a) * b); }",
  "fnCombine"   : "function(partialA, partialB) { return partialA + partialB; }",
  "fnReset"     : "function()                   { return 10; }"
}
```
### 最大值（long）
aggregations.type=lucene_longMax 时，参数：
```
{
    "type":"lucene_longMax",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>" 
}
```
求查询到的值中的最小值，该值类型为64位有符号整数，相当于`max("<fieldName_string>")`   

- name- 求和值的输出名称 
- fieldName- 求总和的列的名称


### 最小值（long）
aggregations.type=lucene_longMin 时，参数：
```
{
    "type":"lucene_longMin",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
}
```
求查询到的值中的最小值，该值类型为64位有符号整数，相当于`min("<fieldName_string>")`   

- name- 求和值的输出名称 
- fieldName- 求总和的列的名称

### 总和（long）
aggregations.type=lucene_longSum 时，参数：
```
{
    "type":"lucene_longSum",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>" 
}
```
将查询到的值的和计算为64位有符号整数，相当于`sum("<fieldName_string>")`   

- name- 求和值的输出名称 
- fieldName- 求总和的列的名称


## <a id="post-aggregation" href="post-aggregation"></a>  postAggregation 聚合
postAggregations.type 可选项: `arithmetic` , `buckets`, `constant` , `customBuckets` ,  `equalBuckets` ,  `fieldAccess` , `hyperUniqueCardinality` , `javascript` , `max` , `min` , `sketchEstimate` , `sketchSetOper`

### 算术

postAggregations.type=arithmetic 时，参数：  
```
{
    "type":"arithmetic",
    "name":"<name_string>",
    "fn":"<fnName_string>",
    "fields":[
    		<postAggregator>,<postAggregator>,...
    ],
    "ordering":"ordering_string"
}
```
算术后聚合器将提供的函数从左到右应用于给定的字段。字段可以是聚合器或其他后期聚合器。

支持的功能有+，-，*，/，和quotient。quotient划分的行为像常规小数点的划分。
- ordering:定义了排序的顺序。

### buckets
postAggregations.type=buckets 时，参数：
```
{
    "type":"buckets",
    "name":"<output_name>",
    "fieldName":"<aggregator_name>",
    "bucketSize":4.5,
    "offset":3.2
}
```
装到给定size相同的bucket里面
- bucketSize:bucket的大小
- offset:抵消

### 常量

postAggregations.type=constant 时，参数：
```
{
    "type":"constant",
    "name":"<output_name>",
    "value":<numerical_value>
}
```
总是返回指定的值。

### 自定义buckets
postAggregations.type=customBuckets 时，参数：
```
{
    "type":"customBuckets",
    "name":"<output_name>",
    "fieldName":"<aggregator_name>",
    "breaks":[
    	1.2,
    	3.5
    ]
}
```
- breaks：bucket的分界点  
装到自定义的bucket里面

### equalBuckets
postAggregations.type=equalBuckets 时，参数：
```
{
    "type":"equalBuckets",
    "name":"<output_name>",
    "fieldName":"<aggregator_name>",
    "numBuckets":20
}
```  


### fieldAccess
postAggregations.type=fieldAccess 时，参数：
```
{
    "type":"fieldAccess",
    "name":"<output_name>",
    "fieldName":"<aggregator_name>"
}
```
返回指定聚合器生成的值。
### hyperUniqueCardinality

postAggregations.type=hyperUniqueCardinality 时，参数：
```
{
    "type":"hyperUniqueCardinality",
    "name":"<output name>",
    "fieldName":"<the name field value of the hyperUnique aggregator>" 
}
```
用于包装hyperUnique对象，以便可以在后期聚合中使用。  

**example**
```
  "aggregations" : [{
    {"type" : "count", "name" : "rows"},
    {"type" : "hyperUnique", "name" : "unique_users", "fieldName" : "uniques"}
}],
"postAggregations" : [{
    "type"   : "arithmetic",
    "name"   : "average_users_per_row",
    "fn"     : "/",
    "fields" : [
      { "type" : "hyperUniqueCardinality", "fieldName" : "unique_users" },
      { "type" : "fieldAccess", "name" : "rows", "fieldName" : "rows" }
    ]
}]
```


### javascript
postAggregations.type=javascript 时，参数：
```
{
    "type":"javascript",
    "name":"<output_name>",
    "fieldNames":[
    	"<aggregator_name>","<aggregator_name>",...
    ],	
    "function":"<javascript function>"  
}
```
将提供的JavaScript函数应用于给定字段。     

**example**
```
{
  "type": "javascript",
  "name": "absPercent",
  "fieldNames": ["delta", "total"],
  "function": "function(delta, total) { return 100 * Math.abs(delta) / total; }"
}
```


### 最大
postAggregations.type=max 时，参数：
```
{
    "type":"max",
    "name":"<output_name>",
    "fieldName":"<post_aggregator>" 
}
```
计算最大值
### 最小
postAggregations.type=min 时，参数：
```
{
    "type":"min",
    "name":"<output_name>",
    "fieldName":"<post_aggregator>" 
}
```
计算最小值

### sketchEstimate
postAggregations.type=sketchEstimate 时，参数：
```
{
    "type":"sketchEstimate",
    "name":"<name_string>",
    "field":{
    	<postAggregator>
    }
}
```
将堆外内存的内容读取出来

### sketchSetOper
postAggregations.type=sketchSetOper 时，参数：
```
{
    "type":"sketchSetOper",
    "name":"<name_string>",
    "func":"<func_string>",
    "size":20,
    "fields":[
        <postAggregator>,<postAggregator>,...
    ] 
}
```
将多个堆外内存的内容进行操作


## <a id="having" href="having"></a> having

相当于having语句  

having.type可选项： and , or , not , greaterThan , lessThan , equalTo , dimSelector , always 

### 或
having.type=or 时，参数：
```
{
    "type":"or",
    "havingSpecs":[
    	<havingSpec>,<havingSpec>,..
    ]
}
```
逻辑或
### 非
having.type=not 时，参数：
```
{
    "type":"not",
    "havingSpecs":{
    	<havingSpec>
    }
}
```
逻辑非
### 大于
having.type=greaterThan 时，参数：
```
{
    "type":"greaterThan",
    "aggregation":"aggName",
    "value":10
}
```

### 小于
having.type=lessThan 时，参数：
```
{
    "type":"lessThan",
    "aggregation":"aggName",
    "value":10
}
```
### 等于
having.type=equalTo 时，参数：
```
{
    "type":"equalTo",
    "aggregation":"aggName",
    "value":10
}
```
### dimSelector
having.type=dimSelector 时，参数：
```
{
    "type":"dimSelector",
    "dimension":"dimName",
    "value":"value_string",
    "extractionFn":{
    	<extractionFn>
    }
}
```
### 总是
having.type=always 时，参数：
```
{
    "type":"always",
}
```
