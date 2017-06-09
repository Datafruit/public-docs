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
  - [`post-aggregation`](#post-aggregation)
  - [`having`](#having)

## <a id="dataSource" href="dataSource"></a> dataSource 数据源

&#160; &#160; &#160; &#160;数据源相当于数据库中的表。     

dataSource.type可选项： table , query , union , 也可以是一个字符串()。

###  1. Table DataSource
&#160; &#160; &#160; &#160;JSON示例如下：    
```
{
    "type":"table",  
    "name":<string_value>
}
```
&#160; &#160; &#160; &#160;最常用的数据源，`<string_value>`为源数据源的名称，类似关系数据库中的表名。

### 2. Union DataSource
&#160; &#160; &#160; &#160;JSON示例如下：
```
{
    "type": "union",
    "dataSources": [<string_value1>,<string_value2>,... ]
}
```
&#160; &#160; &#160; &#160;该数据源连接两个或多个表数据，`<string_value1>` `<string_value2>` 为表数据源的名称。Union DataSource应该有相同的schema。Union Queries应该发送到代理/路由器节点，并不受历史节点直接支持。

### 3. Query DataSource
&#160; &#160; &#160; &#160;JSON示例如下：
```
{
    "type":"query",
    "query":{<query>}   
}
```
&#160; &#160; &#160; &#160;可以进行查询的嵌套。


## <a id="dimension" href="dimension"></a> dimension 维度
&#160; &#160; &#160; &#160;Dimensino ,即维度，可以在查询中使用以下JSON字段来操作维度值。 

### 1. Default Dimension
&#160; &#160; &#160; &#160;Default Dimension 返回维度值，并可选择对维度进行重命名。JSON 示例如下：
```
{
    "type":"default",
    "dimension":"<dimension>",
    "outputName":"<output_name>"
}
```


### 2. Extraction Dimension
&#160; &#160; &#160; &#160;Extraction Dimension 返回使用给定提取函数转换的维度值。JSON示例如下：

```
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


### 3. Regex Dimension
&#160; &#160; &#160; &#160;Regex Dimension 返回给定正则表达式的第一个匹配组。如果没有匹配，则返回维度值。JSON示例如下：
```
{
    "type":"regex",
    "delegate":{
      	<dimensionSpec>
    },
    "pattern":"pattern_string"
}
```
 
&#160; &#160; &#160; &#160;例如，使用`"expr" : "(\\w\\w\\w).*"`将改变'Monday'，'Tuesday'，'Wednesday'成'Mon'，'Tue'，'Wed'。

### 4. ListFiltered Dimension
&#160; &#160; &#160; &#160;ListFiltered Dimension 仅适用于多值维度。JSON示例如下：
```
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
&#160; &#160; &#160; &#160;如果您在druid中有一行具有值为`[“v1”，“v2”，“v3”]`的多值维度，并且通过该维度使用查询过滤器为值“v1” 发送groupBy / topN查询分组。在响应中，您将获得包含“v1”，“v2”和“v3”的3行。对于某些用例，此行为可能不直观。

### 5. Lookup Dimension
&#160; &#160; &#160; &#160;Lookup Dimension 允许在执行提取时使用的一组键和值。JSON示例如下：
```
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
&#160; &#160; &#160; &#160;在查询时可以指定属性 retainMissingValue 为false，并通过设置 replaceMissingValueWith 提示如何处理缺失值。  
&#160; &#160; &#160; &#160;retainMissingValue 如果在查找中找不到，设置为true将使用维度的原始值。
默认是 replaceMissingValueWith = null，retainMissingValue = false 并且导致丢失的值被视为丢失值。

## <a id="interval" href="interval"></a> interval 时间区间

&#160; &#160; &#160; &#160;在查询中指定时间区间。Interval中的时间是ISO-8601格式。对于中国用户，所在时区为东8区，因此需要在时间中加入“+08:00”。 如"2015-12-31T16:00:00+08:00 / 2017-04-14T15:59:59+08:00"。


### 1. Intervals Interval
&#160; &#160; &#160; &#160;JSON示例如下：
```
{
    "type":"intervals",
    "intervals":[<interval>,<interval>,...]
}
```

### 2. Segments Interval
&#160; &#160; &#160; &#160;Segments Interval 可以定义多个段，JSON示例如下：
```
{
    "type":"segments",
    "segments":[
    	{
            "itvl":{<interval>},
            "ver":"version",
            "part":100
        },
        {
            "itvl":{<interval>},
            "ver":"version",
            "part":200
        }
    ]
}
```




## <a id="filter" href="filter"></a> filter 过滤器
&#160; &#160; &#160; &#160;Filter,即过滤器，在查询语句中是一个JSON对象，用来对维度进行筛选，表示维度满足Filter的行是我们需要的数据。它基本上等同于SQL中的WHERE子句。Filter包含如下类型。  

### 1. Seletor Filter
&#160; &#160; &#160; &#160;Seletor Filter是最简单的过滤器，它将与具体值匹配，功能类似于SQL中的where key=value，支持提取功能。Seletor Filter的JSON示例如下：
```
"filter":{
    "type":"selector",
    "dimension":<dimension_string>,
    "value":<value_string>,
    "extractionFn":{<extractionFn>}
}
```
&#160; &#160; &#160; &#160;上面的参数设置这相当于 `WHERE <dimension_string> = <value_string>`   。 

&#160; &#160; &#160; &#160;使用示例如下：
```
"filter": {
    "type": "selector",
    "dimension": "province",
    "value": "广东省"
}
```
&#160; &#160; &#160; &#160;相当于 `WHERE province = ＂广东省＂`。
### 2. Regex Filter
&#160; &#160; &#160; &#160;Regex Filter允许用户用正则表达式来筛选维度，任何标准的Java正则表达式Druid都支持，支持使用提取功能。Regex Filter的JSON示例如下：

```
"filter":{
    "type":"regex",
    "dimension":<dimension_string>,
    "pattern":<pattern_string>,
    "extractionFn":{<extractionFn>}
}
```
- pattern：给定的模式，可以是任何标准的Java正则表达式。

&#160; &#160; &#160; &#160;使用示例如下:
```
"filter": {
  "type": "regex",
  "dimension": "UserID",	
  "pattern": "^c.*"
}
```
&#160; &#160; &#160; &#160;以上实例将匹配任何以"c"开头的"userId"。

### 3. Logical Expression Filer
&#160; &#160; &#160; &#160;Logical Expression Filer包含and、or、not三种过滤器，与SQL中的and、or、not相似。每一种过滤器都支持嵌套，可以构建丰富的逻辑表达式。
#### 3.1 And Filter
&#160; &#160; &#160; &#160;And Filter的JSON示例如下：
```
"filter"：{
    "type":"and",
    "fields":[<filter>, <filter>, ...]
}
```
`<filter>`可以是任何一种过滤器。

使用示例如下：
```
"filter": {
  "type": "and",
  "fields": [
    {
      "type": "selector",
      "dimension": "age",
      "value": 20
    },
    {
      "type": "selector",
      "dimension": "province",
      "value": "广东省"
    }
  ]
}
```
&#160; &#160; &#160; &#160;相当于：`WHERE age=20 AND province="广东省"`

#### 3.2 Or Filter
&#160; &#160; &#160; &#160;Or Filter的JSON示例如下：
```
"filter"：{
    "type":"or",
    "fields":[
    "fields":[<filter>, <filter>, ...]
}
```
`<filter>`可以是任何一种过滤器。

&#160; &#160; &#160; &#160;使用示例如下：
```
"filter": {
  "type": "or",
  "fields": [
    {
      "type": "selector",
      "dimension": "age",
      "value": 20
    },
    {
      "type": "selector",
      "dimension": "province",
      "value": "广东省"
    }
  ]
}
```
&#160; &#160; &#160; &#160;相当于：`WHERE age=20 OR province="广东省"`

#### 3.3 Not Filter
&#160; &#160; &#160; &#160;Not Filter的JSON示例如下：
```
"filter"：{
    "type":"not",
    "field":<filter>
}
```
`<filter>`可以是任何一种过滤器。

&#160; &#160; &#160; &#160;使用示例如下：
```
"filter": {
  "type": "not",
  "field": {
      "type": "selector",
      "dimension": "age",
      "value": 20
  }
}
```
&#160; &#160; &#160; &#160;相当于选出age不等于20的记录。

### 4. Search Filter

&#160; &#160; &#160; &#160;Search Filter通过字符串匹配过滤维度，支持多种匹配方式。Search Filter的JSON示例如下：
```
"filter"：{
  "type":"search",
  "dimension":<dimension_string>,
  "query":{
    "type":"contains",
      "value":<value_string>,
      "caseSensitive":<false | true>
  },
  "extractionFn":{<extractionFn>}
}
```
&#160; &#160; &#160; &#160;使用实例如下：  
```
"filter":{
  "type":"search",
  "dimension":"province",
  "query":{
    "type":"contains",
      "value":"东",
      "caseSensitive":true
  }
}
```
&#160; &#160; &#160; &#160;若省份名字包含"东"字,则匹配。


&#160; &#160; &#160; &#160;Search Query定义了如下几种字符串匹配方式。

**1. contains**  
&#160; &#160; &#160; &#160;如果指定的维度的值包含给定的字符串，则匹配。JSON示例如下：
```
"query":{
    "type":"contains",
    "value":<value_string>,
    "caseSensitive":<false | true>
}
```
caseSensitive：是否大小写敏感

**2.insensitive_contains**  
&#160; &#160; &#160; &#160;如果指定的维度的值包含给定的字符串，则匹配，不区分大小写。相当于contains中的caseSensitive设置为false。insensitive_contains的JSON示例如下：
```
"query":{
    "type":"insensitive_contains",
    "value":<value_string>
}
```
**3. fragment**  
&#160; &#160; &#160; &#160;如果指定的维度的值的任意部分包含给定的字符串，则匹配。fragment的JSON示例如下：
```
"query":{
    "type":"fragment",
    "values":[<value_string>,<value_string>,...],
    "caseSensitive":<false | true>
}
```

**4. regex**  
&#160; &#160; &#160; &#160;如果指定的维度的值与正则表达式匹配，则匹配。regex 的JSON示例如下：
```
"query":{
    "type":"regex",
    "pattern":<pattern_string>
}
```
### 5. In Filter

&#160; &#160; &#160; &#160;In Filter 类似于SQL中的in。In Filter 的 JSON 示例如下：
```
"filter":{
    "type":"in",
    "dimension":<dimension_string>,
    "values":[<value_string>,<value_string>,...],
    "extractionFn":{
    	{extractionFn}
    }
}
```
- values: in的范围。  

&#160; &#160; &#160; &#160;使用实例如下：
```
"filter": {
    "type": "in",
    "dimension": "province",
    "values": [
      "广东省",
      "广西省"
    ]
  }
```
&#160; &#160; &#160; &#160;相当于： `WHERE province IN ("广东省","广西省")`

### 6. Bound Filter
&#160; &#160; &#160; &#160;Bound Filter 其实就是比较过滤器，包含“大于”、“小于”和“等于”三种算子。Bound Filter 默认是字符串比较，并基于字典序。如果要使用数字比较，则需要在查询中设定alphaNumeric的值为true。Bound Filter默认的大小比较为“>=”或“<=”。Bound Filter具体的JSON表达式示例如下：
```
"filter":{
  "type":"bound",
  "dimension":<dimension_string>,
  "lower":"0",
  "upper":"100",
  "lowerStrict":<false | true>,
  "upperStrict":<false | true>,
  "alphaNumeric":<false | true>,
  "extractionFn":{<extractionFn>}
}
```
- lowerStrict：是否包含下界  
- upperStrict：是否包含上界
- alphaNumeric：是否进行数值比较

&#160; &#160; &#160; &#160;使用示例如下：
```
"filter": {
  "type": "bound",
  "dimension": "age",
  "alphaNumeric": true,
  "upper": 20,
  "upperStrict": true
}
```
&#160; &#160; &#160; &#160;相当于：`WHERE age<20 `。

### 7. JavaScript Filter
&#160; &#160; &#160; &#160;如果上述Filter不能满足要求，Druid还可以通过自己写JavaScript Filter来过滤维度，但是只能支持一个入参，就是Filter里指定的维度的值，返回 true 或 false 。JavaScript Filter 的JSON表达式实例如下：

```
"filter":{
    "type":"javascript",
    "dimension":<dimension_string>,
    "function":<function_string>,
    "extractionFn":{<extractionFn>}
}
```
- dimension: 函数的参数（只能有一个）

&#160; &#160; &#160; &#160;使用示例如下：
```
{
  "type":"javascript",
  "dimension":"name",
  "function":"function(x) { return(x >= 'bar' && x <= 'foo') }"
}
```
&#160; &#160; &#160; &#160;上面的例子可匹配任何name在'bar'和'foo'之间的维度值。


### 8. Spatial Filter
&#160; &#160; &#160; &#160;Spatial Filter，即为空间过滤器，JSON表达式示例如下：
```
"filter":{
    "type":"spatial",
    "dimension":<dimension_string>,
    "bound":<bound>
}
```
&#160; &#160; &#160; &#160;spatial.bound.type，即边界类型，目前支持两种： rectangular ，radius

**1. Rectangular**   
&#160; &#160; &#160; &#160;Rectangular,即为矩形，JSON示例如下：
```
"bound":{
    "type":"rectangular",
    "minCoords":[4.5,5.3],
    "maxCoords":[2.3,5.6],
    "limit":50
}
```
- minCoords: 最小坐标轴列表 [x,y,z,...]
- maxCoords: 最大坐标轴列表 [x,y,z,...]

**2. Radius**  
&#160; &#160; &#160; &#160;Radius,即为半径，JSON示例如下：
```
"bound":{
    "type":"radius",
    "coords":[4.5,5.3],
    "radius":[2.3,5.6],
    "limit":50
}
```
- coords: 原点坐标 [x,y,z,...]
- radius: 浮点表示的半径值 [x,y,z,...]

### 9. All Filter
&#160; &#160; &#160; &#160;All Filter 匹配所有维度值，JSON示例如下：
```
{
    "type":"all"
}
```
### 10. Lookup Filter
&#160; &#160; &#160; &#160;JSON示例如下：
```
{
    "type":"lookup",
    "dimension":<dimension_string>,
    "lookup":<lookup_string>
}
```

### 11. lucene Filter
&#160; &#160; &#160; &#160;JSON示例如下：
```
{
    "type":"lucene",
    "query":<query_string>
}
```

## <a id="extraction-fn" href="extraction-fn"></a> extraction-fn 提取过滤器

&#160; &#160; &#160; &#160;Extraction,即提取过滤器，使用一些特定的提取函数匹配维度。  
&#160; &#160; &#160; &#160;extraction类型可选项：time , regex , partial , searchQuery , javascript , timeFormat , identity , lookup , registeredLookup , substring , cascade , stringFormat , upper , lower 

### 1. Regex Extraction
&#160; &#160; &#160; &#160;Regex Extraction 返回给定正则表达式的第一个匹配组。如果没有匹配，则返回维度值。JSON示例如下：
```
"extraction"{
    "type":"regex",
    "expr":<expr_string>,
    "replaceMissingValue":<false | true>,
    "replaceMissingValueWith":<replace_string>
}
```
### 2. Partial Extraction
&#160; &#160; &#160; &#160;如果正则表达式匹配，返回维度值不变，否则返回null。JSON示例如下：
```
"extraction"{
    "type":"partial",
    "expr":<expr_string>
}
```
### 3. SearchQuery Extraction
&#160; &#160; &#160; &#160;如果给定SearchQuerySpec 匹配，返回维度值不变，否则返回null。JSON示例如下：
```
"extraction"{
    "type":"searchQuery",
    "query":{
        "type":"contains",
        "value":<value_string>,
        "caseSensitive":<false | true>
    }
}
```
### 4. Javascript Extraction
&#160; &#160; &#160; &#160;Javascript Extraction 返回由给定的JavaScript函数转换的维度值。JSON示例如下：
```
"extraction"{
    "type":"javascript",
    "query":{
	"type":"contains",
	"function":<function_string>,
      	"injective":<false | true>
    }
}
```

### 5. TimeFormat Extraction
&#160; &#160; &#160; &#160;TimeFormat Extraction 以特定格式，时区或语言环境来提取时间戳。JSON示例如下：
```
"extraction"{
    "type":"timeFormat",
    "query":{
        "type":"contains",
        "format":<pattern_string>,
        "timeZone":{<dateTimeZone>},
        "locale":<locale_string>
    }
}
```
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
### 6. Identity Extraction
filter.extraction.type=identity 时，参数：
```
"extraction"{
    "type":"identity"
}
```

### 7. Lookup Extraction
filter.extraction.type=lookup 时，参数：
```
"extraction"{
    "type":"lookup",
    "lookup": {
	    "lookup":<lookup>, 
	    "retainMissingValue":<false | true> 
	    "replaceMissingValueWith":<replaceMissingValueWith_string>, 
	    "injective":<false | true>, 
	    "optimize":<false | true>
    },    
    "retainMissingValue":<false | true>,	
    "replaceMissingValueWith":<replace_string>,	
    "injective":<false | true>, 
    "optimize":<false | true>
}
```
### 8. RegisteredLookup Extraction
filter.extraction.type=registeredLookup 时，参数：
```
"extraction"{
    "type":"registeredLookup",
    "lookup":<lookup_string>,
    "retainMissingValue":<false | true>,   
    "replaceMissingValueWith":<replace_string>,  
    "injective":<false | true>,  
    "optimize":<false | true>, 
}
```
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
### 9. SubString Extraction
&#160; &#160; &#160; &#160;SubString Extraction 返回从提供的索引开始至所需长度的子字符串。JSON示例如下：
```
"extraction"{
    "type":"substring",
    "index":10,
    "length":20
}
```
### 10. Cascade Extraction
&#160; &#160; &#160; &#160;Cascade Extraction 按指定的顺序将指定的提取函数转换为维度值。JSON示例如下：
```
"extraction"{
    "type":"cascade",
    "extractionFns":[{<extraction>},{<extraction>}]
}
```
### 11. StringFormat Extraction
&#160; &#160; &#160; &#160;StringFormat Extraction 返回根据给定的格式字符串格式化的维度值。JSON示例如下：
```
"extraction"{
    "type":"stringFormat",
    "format":<format_string>,
    "nullHandling":{<nullHandling>}  
}
```
### 12. Upper Extraction
&#160; &#160; &#160; &#160; Upper Extraction 返回大写的维度值。JSON示例如下：
```
"extraction"{
    "type":"upper",
    "locale":<locale_string>
}
```
### 13. Lower Extraction
&#160; &#160; &#160; &#160; Lower Extraction 返回小写的维度值。JSON示例如下：
```
"extraction"{
    "type":"lower",
    "locale":<locale_string>
}
```


## <a id="aggregation" href="aggregation"></a> aggregation 聚合

&#160; &#160; &#160; &#160;Aggregation，即聚合器。若在摄入阶段就指定，则会在roll up 时就进行计算；当然，也能在查询时指定。聚合器包含以下几种类型。

### 1. Count Aggregation
&#160; &#160; &#160; &#160;用于计算Druid的数据行数，相当于`count()`。Count Aggregation 的JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_count",
    "name":<name_string>
  }
]
```
使用示例如下:
```
"aggregations": [
  {
    "type": "lucene_count",
    "name": "__VALUE__"
  }
]
```

### 2. Cardinality Aggregator
&#160; &#160; &#160; &#160;废弃。在查询时，Cardinality Aggregation 使用HyperLogLog算法计算给定维度集合的基数，相当于distinct()。Cardinality Aggregation 的JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_cardinality",
    "name":<name_string>,
    "fieldNames":[<fieldName_string>,<fieldName_string>,...], 
    "byRow":<false | true> 
  }
]
```
&#160; &#160; &#160; &#160;当设置byRow为false（默认值）时，它计算由所有给定维度的所有维度值的并集组成的集合的基数。

### 3. HyperUnique Aggregator
&#160; &#160; &#160; &#160;在查询时，HyperUnique Aggregation 使用HyperLogLog算法计算给定维度集合的基数。HyperUnique Aggregation比 Cardinality Aggregation要快得多，因为HyperUnique Aggregation在摄入阶段就会为Metric做聚合，因此在通常情况下，对于单个维度求基数，比较推荐使用 HyperUnique Aggregation。JSON示例如下：

```
"aggregations":[
  {
    "type":"lucene_hyperUnique",
    "name":<name_string>,
    "fieldName":<fieldName_string>
  }
]
```
&#160; &#160; &#160; &#160;使用示例如下:
```
"aggregations":[
  {
    "type":"lucene_hyperUnique",
    "name":"ageCount",
    "fieldName":"age"
  }
]
```

&#160; &#160; &#160; &#160;查询结果如下:
```
[
  {
    "timestamp": "2017-01-01T00:00:00.000Z",
    "result": {
      "user": 20.098296726168925
    }
  }
]
```




### 4. DoubleMax Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最大值，该值类型为 double ，输入的值类型为 float ,相当于`max(<fieldName_string>)`。JSON示例如下：
```
"aggregations":[
  {
    "type":"lucene_doubleMax",
    "name":<name_string>,
    "fieldName":<fieldName_string>
 }
]
```
- name- 求最大值的输出名称 
- fieldName- 求最大值的列的名称

&#160; &#160; &#160; &#160;使用示例如下:
```
"aggregations": [
  {
  "type":"lucene_doubleMax",
  "name":"max",
  "fieldName":"age"
  }
]
```
&#160; &#160; &#160; &#160;返回结果如下:
```
[
  {
    "timestamp": "2017-01-01T00:00:00.000Z",
    "result": {
      "max": 29
    }
  }
]
```

### 5. DoubleMin Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最小值，该值类型为double，输入的值类型为 float ,相当于`min(<fieldName_string>)`。JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_doubleMin",
    "name":<name_string>,
    "fieldName":<fieldName_string>
  }
]
```
- name- 求最小值的输出名称 
- fieldName- 求最小值的列的名称

###  6. DoubleSum Aggregation
&#160; &#160; &#160; &#160;将查询到的值的和计算为double类型的数，输入的值类型为 float ,相当于`sum(<fieldName_string>)`。JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_doubleSum",
    "name":<name_string>,
    "fieldName":<fieldName_string> 
  }
]
```
- name- 求和值的输出名称 
- fieldName- 求总和的列的名称

### 7. LongMax Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最大值，该值类型为64位有符号整数，相当于`max(<fieldName_string>)`。JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_longMax",
    "name":<name_string>,
    "fieldName":<fieldName_string>
  }
]
```
- name- 求最大值的输出名称 
- fieldName- 求最大值的列的名称


### 8. LongMin Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最小值，该值类型为64位有符号整数，相当于`min(<fieldName_string>)`。JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_longMin",
    "name":<name_string>,
    "fieldName":<fieldName_string>
  }
]
```
- name- 求最小值的输出名称 
- fieldName- 求最小值的列的名称

### 9. LongSum Aggregation
&#160; &#160; &#160; &#160;将查询到的值的和计算为64位有符号整数，相当于`sum(<fieldName_string>)` 。JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_longSum",
    "name":<name_string>,
    "fieldName":<fieldName_string> 
  }
]
```
- name- 求和值的输出名称 
- fieldName- 求总和的列的名称


### 10. Javascript Aggregation

&#160; &#160; &#160; &#160;如果上述聚合器无法满足需求，Druid还提供了JavaScript Aggregation。用户可以自己写JavaScript function，其中指定的列即为function的入参。JavaScript Aggregation 的JSON示例如下：

```
"aggregations": [
  {
    "type":"lucene_javascript",
    "name":<name_string>,
    "fieldNames":[<fieldName_string>,<fieldName_string>], 
    "fnAggregate":<fnAggregate_string>, 
    "fnReset":<fnReset_string>, 
    "fnCombine":<fnCombine_string> 
  }
]
```
- name:这组JavaScript函数的名称
- fieldNames:参数的名字  

&#160; &#160; &#160; &#160;使用示例如下:
```
"aggregations": [
  {
    "type": "lucene_javascript",
    "name": "sum(log(x)*y) + 10",
    "fieldNames": ["x", "y"],
    "fnAggregate" : "function(current, a, b)      { return current + (Math.log(a) * b); }",
    "fnCombine"   : "function(partialA, partialB) { return partialA + partialB; }",
    "fnReset"     : "function()                   { return 10; }"
  }
]
```

### 11. DateMin Aggregation

&#160; &#160; &#160; &#160;求查询到的值中的最小值，该值类型为 date , 输入的值的类型必须是 date 。DateMin Aggregation 的JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_dateMin",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
  }
]
```
&#160; &#160; &#160; &#160;使用示例如下:
```
"aggregations": [
  {
    "type":"lucene_dateMin",
    "name":"minDate",
    "fieldName":"birthday"
  }
]
```
&#160; &#160; &#160; &#160;查询结果如下:
```
[
  {
    "timestamp": "2017-01-01T00:00:00.000Z",
    "result": {
      "minDate": "1988-05-17T07:36:52.046Z"
    }
  }
]
```



### 12. DateMax Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最小值，该值类型为 date ,输入的值的类型必须是 date 。DateMax Aggregation 的JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_dateMax",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
  }
]
```
### 13. Filtered Aggregation

&#160; &#160; &#160; &#160;Filtered Aggregation 可以在 aggregation 中指定 Filter 规则。只对满足规则的维度进行聚合，以提升聚合效率。JSON示例如下：
```
"aggregations": [
  {
    "type":"lucene_filtered",
    "aggregator":<aggregator>, 
    "filter":"<filter>
  }
]
```
&#160; &#160; &#160; &#160;使用示例如下:
```
"aggregations":[
  {
    "type":"lucene_filtered",
    "aggregator":  {
	    "type": "lucene_count",
	    "name": "__VALUE__"
	  },
    "filter": {
	    "type": "bound",
	    "dimension": "age",
	    "alphaNumeric": true,
	    "upper": 20,
	    "upperStrict": true
	  }
  }	
]
```

&#160; &#160; &#160; &#160;该聚合只对 age>20 的记录实行。

&#160; &#160; &#160; &#160;查询结果如下:
```
[
  {
    "timestamp": "2017-01-01T00:00:00.000Z",
    "result": {
      "__VALUE__": 50094
    }
  }
]
```

### 14. ThetaSketch Aggregation
&#160; &#160; &#160; &#160;ThetaSketch Aggregation 的 JSON 示例如下：
```
"aggregations": [
  {
    "type":"lucene_thetaSketch",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
    "size":10,
    "shouldFinalize":true,
    "isInputThetaSketch":true,
    "errorBoundsStdDev":5,
    "trunc":true
  }
]
```


## <a id="post-aggregation" href="post-aggregation"></a>  postAggregation 
&#160; &#160; &#160; &#160;PostAggregation 可以对 Aggregation 的结果进行二次加工并输出。最终的输出既包含 Aggregation 的结果，也包含 PostAggregation的结果。使用PostAggregation 必须包含 Aggregation。PostAggregation 包含如下类型：  

### 1. Arithmetic PostAggregation
&#160; &#160; &#160; &#160;Arithmetic PostAggregation 支持对Aggregation的结果和其他 Arithmetic PostAggregation 的结果进行“ + ”，“ - ”，“ * ”，“ / ”和“ quotient ”计算，quotient 划分的行为像常规小数点的划分。
  
```
"postAggregations":{
    "type":"arithmetic",
    "name":<name_string>,
    "fn":<fnName_string>,
    "fields":[<postAggregator>,<postAggregator>...],
    "ordering":<ordering_string>
}
```
- 对于“/”，如果分母为0，则返回0。
- “quotient”不判断分母是否为0。
- 当 Arithmetic PostAggregation 的结果参与排序时，默认使用float类型。用户可以手动通过Ordering字段指定排序方式。

&#160; &#160; &#160; &#160;使用示例如下:
```
"postAggregations":[
  {
    "type":"arithmetic",
    "name":"age",
    "fn":"-",
    "fields":[
      {
        "type":"hyperUniqueCardinality",
        "fieldName":"max(age)"
      },
      {
        "type":"hyperUniqueCardinality",
        "fieldName":"min(age)" 
      }
    ],
    "ordering":<ordering_string>
  }
]
```
&#160; &#160; &#160; &#160;以上示例可以计算最大年龄和最小年龄之间的年龄差。

### 2. FieldAccess PostAggregation
&#160; &#160; &#160; &#160;FieldAccess PostAggregation 返回指定的 Aggregation 的值，在 PostAggregation 中大部分情况下使用 fieldAccess 来访问 Aggregation。在fieldName中指定 Aggregation 里定义的 name，如果对HyperUnique 的结果进行访问，则需要使用hyperUniqueCardinality。FieldAccess PostAggregation 的 JSON 示例如下：
```
"postAggregations":[
  {
    "type":"fieldAccess",
    "name":<output_name>,
    "fieldName":<aggregator_name>
  }
]
```

&#160; &#160; &#160; &#160;使用示例如下:
```
"postAggregations":[
  {
    "type":"fieldAccess",
    "name":"field",
    "fieldName":"__VALUE__"
  }
]
```
&#160; &#160; &#160; &#160;结果如下:
```
[
  {
    "timestamp": "2017-01-01T00:00:00.000Z",
    "result": {
      "field": 100000,
      "__VALUE__": 100000
    }
  }
]
```


### 3. Constant PostAggregation
&#160; &#160; &#160; &#160;Constant PostAggregation 会多返回一个常数，比如100。可以将 Aggregation 返回的结果转换为百分比。JSON示例如下：
```
"postAggregations":[
  {
    "type":"constant",
    "name":<output_name>,
    "value":<numerical_value>
  }
]
```
&#160; &#160; &#160; &#160;使用示例如下:
```
"postAggregations":[
  {
    "type":"constant",
    "name":"num",
    "value":10
  }
]
```
&#160; &#160; &#160; &#160;结果如下:
```
[
  {
    "timestamp": "2017-01-01T00:00:00.000Z",
    "result": {
      "num": 10,
      "__VALUE__": 100000
    }
  }
]
```

### 4. HyperUniqueCardinality PostAggregation
&#160; &#160; &#160; &#160;HyperUniqueCardinality PostAggregation 得到 HyperUnique Aggregation 的结果，使之参与到PostAggregation 的计算中。JSON示例如下：  
```
"postAggregations":[
  {
    "type":"hyperUniqueCardinality",
    "name":<output name>,
    "fieldName":<the name field value of the hyperUnique aggregator>
  }
]
```

&#160; &#160; &#160; &#160;使用示例如下:
```


```
### 5. DataSketch PostAggregation

&#160; &#160; &#160; &#160;Druid DataSketch是基于Yahoo开源的Sketch包实现的数据近似计算功能。    
#### 5.1 SketchEstimate PostAggregation
&#160; &#160; &#160; &#160;SketchEstimate PostAggregation用于计算Sketch的估计值，JSON示例如下：
```
"postAggregations":[
  {
    "type":"sketchEstimate",
    "name":"<name_string>",
    "field":{<postAggregator>}
  }
]
```

#### 5.2 SketchSetOper PostAggregation
&#160; &#160; &#160; &#160;SketchSetOper PostAggregation用于Sketch的集合运算，JSON示例如下：
```
"postAggregations":[
  {
    "type":"sketchSetOper",
    "name":"<name_string>",
    "func":"<func_string>",
    "size":20,
    "fields":[<postAggregator>,<postAggregator>,...] 
  }
]
```

### 6. Buckets PostAggregation
&#160; &#160; &#160; &#160;Buckets PostAggregation 的 JSON 示例如下：
```
"postAggregations":[
  {
    "type":"buckets",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>",
    "bucketSize":4.5,
    "offset":3.2
  }
]
```
- bucketSize: bucket的大小
- offset: bucket的偏移量


### 7. CustomBuckets PostAggregation
&#160; &#160; &#160; &#160;CustomBuckets PostAggregation 的 JSON 示例如下：
```
"postAggregations":[
  {
    "type":"customBuckets",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>",
    "breaks":[1.2,3.5]
  }
]
```

### 8. EqualBuckets PostAggregation
&#160; &#160; &#160; &#160;EqualBuckets PostAggregation 的 JSON 示例如下：：
```
"postAggregations":[
  {
    "type":"equalBuckets",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>",
    "numBuckets":20
  }
]
```  




### 9. Javascript PostAggregation
&#160; &#160; &#160; &#160;Javascript PostAggregation 将提供的 JavaScript 函数应用于给定字段，JSON示例如下：
```
"postAggregations":[
  {
    "type":"javascript",
    "name":"<output_name>",
    "fieldNames":["<aggregator_name>","<aggregator_name>",...],	
    "function":"<javascript function>"  
  }
]
```

&#160; &#160; &#160; &#160;使用示例如下:
```
"postAggregations":[
  {
    "type": "javascript",
    "name": "absPercent",
    "fieldNames": ["delta", "total"],
    "function": "function(delta, total) { return 100 * Math.abs(delta) / total; }"
  }
]
```


### 10. Max PostAggregation
&#160; &#160; &#160; &#160;Max PostAggregation用于计算最大值，JSON示例如下：
```
"postAggregations":[
  {
    "type":"max",
    "name":"<output_name>",
    "fieldName":"<post_aggregator>" 
 }
]
```

### 11. Min PostAggregation
&#160; &#160; &#160; &#160;Min PostAggregation用于计算最小值，JSON示例如下：
```
"postAggregations":[
  {
    "type":"min",
    "name":"<output_name>",
    "fieldName":"<post_aggregator>" 
  }
]
```


## <a id="having" href="having"></a> having

&#160; &#160; &#160; &#160;类似于SQL中的 having 操作，对 GroupBy 的结果进行筛选。支持多种操作：  

### 1. 逻辑表达式过滤器
#### 1.1 And
&#160; &#160; &#160; &#160;和，JSON示例如下：
```
{
    "type":"and",
    "havingSpecs":[<havingSpec>,<havingSpec>,..]
}
```

#### 1.2 Or
&#160; &#160; &#160; &#160;或，JSON示例如下：
```
{
    "type":"or",
    "havingSpecs":[<havingSpec>,<havingSpec>,..]
}
```

#### 1.3 Not
&#160; &#160; &#160; &#160;非，JSON示例如下：
```
{
    "type":"not",
    "havingSpecs":{<havingSpec>}
}
```
### 2. 数值过滤器

#### 2.1 EqualTo
&#160; &#160; &#160; &#160;等于，JSON示例如下：
```
{
    "type":"equalTo",
    "aggregation":"aggName",
    "value":10
}
```

#### 2.2 GreaterThan
&#160; &#160; &#160; &#160;大于，JSON示例如下：
```
{
    "type":"greaterThan",
    "aggregation":"aggName",
    "value":10
}
```

#### 2.3 LessThan 
&#160; &#160; &#160; &#160;小于，JSON示例如下：
```
{
    "type":"lessThan",
    "aggregation":"aggName",
    "value":10
}
```

### 3. DimSelector
&#160; &#160; &#160; &#160;DimSelector 将匹配尺寸值等于指定值的行，JSON示例如下：
```
{
    "type":"dimSelector",
    "dimension":"dimName",
    "value":<value_string>,
    "extractionFn":{<extractionFn>}
}
```
### 4. Always
&#160; &#160; &#160; &#160;总是，即不进行筛选，全部返回，JSON示例如下：
```
{
    "type":"always",
}
```