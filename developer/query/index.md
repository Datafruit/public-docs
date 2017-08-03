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

数据源相当于数据库中的表。     

- `DataSource` 类别详情如下：
  - [`Table`](#Table)
  - [`Union`](#Union)
  - [`Query`](#Query)  

  也可以是一个字符串。

### <a id="Table" href="Table"></a>  1. `Table DataSource`
`JSON`示例如下：    
```
{
    "type":"table",  
    "name":<string_value>
}
```
最常用的数据源，`<string_value>`为源数据源的名称，类似关系数据库中的表名。

### <a id="Union" href="Union"></a> 2. `Union DataSource`
`JSON`示例如下：
```
{
    "type": "union",
    "dataSources": [<string_value1>,<string_value2>,... ]
}
```
该数据源连接两个或多个表数据，`<string_value1>` `<string_value2>` 为表数据源的名称。`Union DataSource`应该有相同的`schema`。`Union Queries`应该发送到代理/路由器节点，并不受历史节点直接支持。

### <a id="Query" href="Query"></a> 3. `Query DataSource`
`JSON`示例如下：
```
{
    "type":"query",
    "query":{<query>}   
}
```
可以进行查询的嵌套。


## <a id="dimension" href="dimension"></a> dimension 维度
`Dimension` ,即维度。
- `Dimension` 类别详情如下：
  - [`Default`](#Default)
  - [`Extraction`](#Extraction)
  - [`Regex`](#Regex)
  - [`ListFiltered`](#ListFiltered)
  - [`Lookup`](#Lookup)
  - [`NumericGroup`](#NumericGroup)
  - [`CustomGroup`](#CustomGroup)

### <a id="Default" href="Default"></a>1. `Default Dimension`
`Default Dimension` 返回维度值，并可选择对维度进行重命名。`JSON`示例如下：
```
{
    "type":"default",
    "dimension":"<dimension>",
    "outputName":"<output_name>"
}
```


### <a id="Extraction" href="Extraction"></a>2.`Extraction Dimension`
`Extraction Dimension` 返回使用给定提取函数转换的维度值。`JSON`示例如下：

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


### <a id="Regex" href="Regex"></a>3. `Regex Dimension`
`Regex Dimension`返回给定正则表达式的第一个匹配组。如果没有匹配，则返回维度值。`JSON`示例如下：
```
{
    "type":"regex",
    "delegate":{
      	<dimensionSpec>
    },
    "pattern":"pattern_string"
}
```
 
例如，使用`"expr" : "(\\w\\w\\w).*"`将改变`'Monday'`，`'Tuesday'`，`'Wednesday'`成`'Mon'`，`'Tue'`，`'Wed'`。

### <a id="ListFiltered" href="ListFiltered"></a>4. `ListFiltered Dimension`
`ListFiltered Dimension`仅适用于多值维度。`JSON`示例如下：
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
如果您在`druid`中有一行具有值为`[“v1”，“v2”，“v3”]`的多值维度，并且通过该维度使用查询过滤器为值`“v1”` 发送`groupBy / topN`查询分组。在响应中，您将获得包含`“v1”`，`“v2”`和`“v3”`的3行。对于某些用例，此行为可能不直观。

### <a id="Lookup" href="Lookup"></a> 5. `Lookup Dimension`
`Lookup Dimension`允许在执行提取时使用的一组键和值。`JSON`示例如下：  
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
在查询时可以指定属性`retainMissingValue`为`false`，并通过设置`replaceMissingValueWith`提示如何处理缺失值。  
`retainMissingValue`如果在查找中找不到，设置为`true`将使用维度的原始值。
默认是`replaceMissingValueWith = null`，`retainMissingValue = false`并且导致丢失的值被视为丢失值。

### <a id="NumericGroup" href="NumericGroup"></a> 6. `NumericGroup Dimension`

`NumericGroup Dimension`可以对维度进行数字分组。`JSON`示例如下：
```
{	
     "type": "numericGroup",
     "dimension": "<dimensionName>",
     "outputName": "<dimensionOutputName>",
     "min": 1492153157000, 
     "max": 1501229310000, 
     "interval": 86400000,
     "granularity": {
         "period": "P1D",
         "type": "period"
     }
}
```
`min`和`max`为数字的边界，`interval` 为每个分组的长度，这里`period`为`P1D`,即为以一天为周期进行聚合。

### <a id="CustomGroup" href="CustomGroup"></a> 7. `CustomGroup Dimension`

`CustomGroup Dimension`可以对维度进行自定义分组。`JSON`示例如下：  

```
{
    "type": "customGroup",
    "dimension": "event_time",
    "outputName": "test_time",
    "groups": [
        {
          "name": "2017-06-01~2017-06-02",
          "lower": 1496246400000,
          "upper": 1496332800000
        },
        {
          "name": "2017-06-03~2017-06-04",
          "lower": 1499011200000,
          "upper": 1499097600000
        }
    ],
    "outOfBound": true
}
```
`groups`为分组列表，可以存放多个分组。其中，`name`为分组的名字，`lower`和`upper`为边界。`outOfBound`超出边界的是否存放到另外一个分组里。

## <a id="interval" href="interval"></a> interval 时间区间

在查询中指定时间区间。`Interval`中的时间是`ISO-8601`格式。对于中国用户，所在时区为东8区，因此需要在时间中加入“+08:00”。 如"2015-12-31T16:00:00+08:00 / 2017-04-14T15:59:59+08:00"。
- `Interval` 类别详情如下：
  - [`Intervals`](#Intervals)
  - [`Segments`](#Segments)


### <a id="Intervals" href="Intervals"></a> 1. `Intervals Interval`
`JSON`示例如下：
```
{
    "type":"intervals",
    "intervals":[<interval>,<interval>,...]
}
```

### <a id="Segments" href="Segments"></a>2. `Segments Interval`
`Segments Interval`可以定义多个段，`JSON`示例如下：
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
`Filter`,即过滤器，在查询语句中是一个`JSON`对象，用来对维度进行筛选，表示维度满足`Filter`的行是我们需要的数据。它基本上等同于`SQL`中的`WHERE`子句。
- `Filter` 类别详情如下：
  - [`Seletor`](#Filter-Seletor)
  - [`Regex`](#Filter-Regex)
  - [`And`](#Filter-And)
  - [`Or`](#Filter-Or)
  - [`Not`](#Filter-Not)
  - [`Search`](#Filter-Search)
  - [`In`](#Filter-In)
  - [`Bound`](#Filter-Bound)
  - [`JavaScript`](#Filter-JavaScript)
  - [`Spatial`](#Filter-Spatial)
  - [`All`](#Filter-All)
  - [`Lookup`](#Filter-Lookup)
  - [`Lucene`](#Filter-Lucene)  
  
### <a id="Filter-Seletor" href="Filter-Seletor"></a>1. `Seletor Filter`
`Seletor Filter`是最简单的过滤器，它将与具体值匹配，功能类似于`SQL`中的`where key=value`，支持提取功能。`Seletor Filter`的`JSON`示例如下：
```
"filter":{
    "type":"selector",
    "dimension":<dimension_string>,
    "value":<value_string>,
    "extractionFn":{<extractionFn>}
}
```
上面的参数设置这相当于 `WHERE <dimension_string> = <value_string>`。 

使用示例如下：
```
"filter": {
    "type": "selector",
    "dimension": "province",
    "value": "广东省"
}
```
相当于 `WHERE province = ＂广东省＂`。
### <a id="Filter-Regex" href="Filter-Regex"></a>2. `Regex Filter` 
`Regex Filter`允许用户用正则表达式来筛选维度，任何标准的`Java`正则表达式`Druid`都支持，支持使用提取功能。`Regex Filter`的`JSON`示例如下：

```
"filter":{
    "type":"regex",
    "dimension":<dimension_string>,
    "pattern":<pattern_string>,
    "extractionFn":{<extractionFn>}
}
```
- `pattern`：给定的模式，可以是任何标准的`Java`正则表达式。

使用示例如下:
```
"filter": {
    "type": "regex",
    "dimension": "UserID",	
    "pattern": "^c.*"
}
```
以上实例将匹配任何以`"c"`开头的`"userId"`。

### 3. `Logical Expression Filer`
`Logical Expression Filer`包含`and`、`or`、`not`三种过滤器，与`SQL`中的`and`、`or`、`not`相似。每一种过滤器都支持嵌套，可以构建丰富的逻辑表达式。
#### <a id="Filter-And" href="Filter-And"></a>3.1 `And Filter`
`And Filter`的`JSON`示例如下：
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
相当于：`WHERE age=20 AND province="广东省"`

#### <a id="Filter-Or" href="Filter-Or"></a> 3.2 `Or Filter`
`Or Filter`的`JSON`示例如下：
```
"filter"：{
    "type":"or",
    "fields":[
    "fields":[<filter>, <filter>, ...]
}
```
`<filter>`可以是任何一种过滤器。

使用示例如下：
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
相当于：`WHERE age=20 OR province="广东省"`

#### <a id="Filter-Not" href="Filter-Not"></a> 3.3 `Not Filter`
`Not Filter`的`JSON`示例如下：
```
"filter"：{
    "type":"not",
    "field":<filter>
}
```
`<filter>`可以是任何一种过滤器。

使用示例如下：
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
相当于选出`age`不等于20的记录。

### <a id="Filter-Search" href="Filter-Search"></a> 4. `Search Filter`

`Search Filter`通过字符串匹配过滤维度，支持多种匹配方式。`Search Filter`的`JSON`示例如下：
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
使用实例如下：  
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
若省份名字包含"东"字,则匹配。


`Search Query`定义了如下几种字符串匹配方式。

**1. contains**  
如果指定的维度的值包含给定的字符串，则匹配。`JSON`示例如下：
```
"query":{
    "type":"contains",
    "value":<value_string>,
    "caseSensitive":<false | true>
}
```
`caseSensitive`：是否大小写敏感

**2.insensitive_contains**  
如果指定的维度的值包含给定的字符串，则匹配，不区分大小写。相当于`contains`中的`caseSensitive`设置为`false`。`insensitive_contains`的`JSON`示例如下：
```
"query":{
    "type":"insensitive_contains",
    "value":<value_string>
}
```
**3. fragment**  
如果指定的维度的值的任意部分包含给定的字符串，则匹配。`fragment`的`JSON`示例如下：
```
"query":{
    "type":"fragment",
    "values":[<value_string>,<value_string>,...],
    "caseSensitive":<false | true>
}
```

**4. regex**  
如果指定的维度的值与正则表达式匹配，则匹配。`regex`的`JSON`示例如下：
```
"query":{
    "type":"regex",
    "pattern":<pattern_string>
}
```
### <a id="Filter-In" href="Filter-In"></a> 5. `In Filter`

`In Filter`类似于`SQL`中的`in`。只支持字符串类型的维度。`In Filter`的`JSON`示例如下：
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

使用实例如下：
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
相当于： `WHERE province IN ("广东省","广西省")`

### <a id="Filter-Bound" href="Filter-Bound"></a> 6. `Bound Filter`
`Bound Filter` 其实就是比较过滤器，包含“大于”、“小于”和“等于”三种算子。`Bound Filter` 默认是字符串比较，并基于字典序。如果要使用数字比较，则需要在查询中设定`alphaNumeric`的值为`true`。`Bound Filter`默认的大小比较为“>=”或“<=”。`Bound Filter`具体的`JSON`表达式示例如下：
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
- `lowerStrict`：是否包含下界  
- `upperStrict`：是否包含上界
- `alphaNumeric`：是否进行数值比较

使用示例如下：
```
"filter": {
    "type": "bound",
    "dimension": "age",
    "alphaNumeric": true,
    "upper": 20,
    "upperStrict": true
}
```
相当于：`WHERE age<20 `。

### <a id="Filter-JavaScript" href="Filter-JavaScript"></a> 7. `JavaScript Filter`
如果上述`Filter`不能满足要求，`Druid`还可以通过自己写`JavaScript Filter`来过滤维度，但是只能支持一个入参，就是`Filter`里指定的维度的值，返回`true`或`false`。`JavaScript Filter`的`JSON`表达式实例如下：

```
"filter":{
    "type":"javascript",
    "dimension":<dimension_string>,
    "function":<function_string>,
    "extractionFn":{<extractionFn>}
}
```
- `dimension`: 函数的参数（只能有一个）

使用示例如下：
```
{
    "type":"javascript",
    "dimension":"name",
    "function":"function(x) { return(x >= 'bar' && x <= 'foo') }"
}
```
上面的例子可匹配任何`name`在`'bar'`和`'foo'`之间的维度值。


### <a id="Filter-Spatial" href="Filter-Spatial"></a> 8. `Spatial Filter`
`Spatial Filter`，即为空间过滤器，`JSON`表达式示例如下：
```
"filter":{
    "type":"spatial",
    "dimension":<dimension_string>,
    "bound":<bound>
}
```
`spatial.bound.type`，即边界类型，目前支持两种：`rectangular`，`radius`

**1. Rectangular**   
`Rectangular`,即为矩形，`JSON`示例如下：
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
`Radius`,即为半径，`JSON`示例如下：
```
"bound":{
    "type":"radius",
    "coords":[4.5,5.3],
    "radius":[2.3,5.6],
    "limit":50
}
```
- `coords`: 原点坐标 [x,y,z,...]
- `radius`: 浮点表示的半径值 [x,y,z,...]

### <a id="Filter-All" href="Filter-All"></a> 9. `All Filter`
`All Filter`匹配所有维度值，`JSON`示例如下：
```
{
    "type":"all"
}
```
### <a id="Filter-Lookup" href="Filter-Lookup"></a> 10. `Lookup Filter`
`Lookup Filter`用于检查该维度的值是否存在于指定的用户分群中。`JSON`示例如下：
```
{
    "type":"lookup",
    "dimension":<dimension_string>,
    "lookup":<lookup_string>
}
```
- `dimension`: 维度名，一般是用户id或设备id。
- `lookup`: 用户分群id  
使用示例如下：
```
{
    "type":"lookup",
    "dimension":"userId",
    "lookup":"usergroup-gdsfrex1"
}
```
### <a id="Filter-Lucene" href="Filter-Lucene"></a>11. `Lucene Filter`
`Lucene Filter`支持`lucene`格式的查询语法，用于过滤不满足条件的数据。`JSON`示例如下：
```
{
    "type":"lucene",
    "query":<query_string>
}
```
- `query`: 满足`lucene`格式的查询字符串。  
1.使用示例如下：
```
{
    "type":"lucene",
    "query":"userId:10001"
}
```
查询`userId=10001`的记录，相当于`WHERE userId='10001'`。    
2.使用`lucene`查询实现过滤维度值不为`null`的记录，示例如下：
```
{
    "type": "not",
    "field": {
    "type": "lucene",
    "query": "(*:* NOT address:*)"
    }
}
```
查询`address`不为`null`的记录，相当于`where address is not null`。  

## <a id="extraction-fn" href="extraction-fn"></a> extraction-fn 提取过滤器

`Extraction`,即提取过滤器，使用一些特定的提取函数匹配维度。  
- `Extraction` 类别详情如下：
  - [`Regex`](#Extraction-Regex)
  - [`Partial`](#Extraction-Partial)
  - [`SearchQuery`](#Extraction-SearchQuery)
  - [`Javascript`](#Extraction-Javascript)
  - [`TimeFormat`](#Extraction-TimeFormat)
  - [`Identity`](#Extraction-Identity)
  - [`Lookup`](#Extraction-Lookup)
  - [`RegisteredLookup`](#Extraction-RegisteredLookup)
  - [`SubString`](#Extraction-SubString)
  - [`Cascade`](#Extraction-Cascade)
  - [`StringFormat`](#Extraction-StringFormat)
  - [`Upper`](#Extraction-Upper)
  - [`Lower`](#Extraction-Lower)


### <a id="Extraction-Regex" href="Extraction-Regex"></a> 1.`Regex Extraction`
`Regex Extraction`返回给定正则表达式的第一个匹配组。如果没有匹配，则返回维度值。`JSON`示例如下：
```
"extractionFn":{
    "type":"regex",
    "expr":<expr_string>,
    "replaceMissingValue":<false | true>,
    "replaceMissingValueWith":<replace_string>
}
```
### <a id="Extraction-Partial" href="Extraction-Partial"></a> 2. `Partial Extraction`
如果正则表达式匹配，返回维度值不变，否则返回`null`。`JSON`示例如下：
```
"extractionFn":{
    "type":"partial",
    "expr":<expr_string>
}
```
### <a id="Extraction-SearchQuery" href="Extraction-SearchQuery"></a> 3. `SearchQuery Extraction`
如果给定`SearchQuerySpec`匹配，返回维度值不变，否则返回`null`。`JSON`示例如下：
```
"extractionFn":{
    "type":"searchQuery",
    "query":{
        "type":"contains",
        "value":<value_string>,
        "caseSensitive":<false | true>
    }
}
```
### <a id="Extraction-Javascript" href="Extraction-Javascript"></a> 4. `Javascript Extraction`
`Javascript Extraction`返回由给定的`JavaScript`函数转换的维度值。`JSON`示例如下：
```
"extractionFn":{
    "type":"javascript",
    "query":{
        "type":"contains",
        "function":<function_string>,
        "injective":<false | true>
    }
}
```

### <a id="Extraction-TimeFormat" href="Extraction-TimeFormat"></a> 5. `TimeFormat Extraction`
`TimeFormat Extraction`以特定格式，时区或语言环境来提取时间戳。`JSON`示例如下：
```
"extractionFn":{
    "type":"timeFormat",
    "query":{
        "type":"contains",
        "format":<pattern_string>,
        "timeZone":{<dateTimeZone>},
        "locale":<locale_string>
    }
}
```
- `timeZone`:时区
- `locale`:地点

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
### <a id="Extraction-Identity" href="Extraction-Identity"></a> 6. `Identity Extraction`
`JSON`示例如下：
```
"extractionFn":{
    "type":"identity"
}
```

### <a id="Extraction-Lookup" href="Extraction-Lookup"></a>7. `Lookup Extraction`
`JSON`示例如下：
```
"extractionFn":{
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

使用示例如下:
```
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
```


### <a id="Extraction-RegisteredLookup" href="Extraction-RegisteredLookup"></a>8. `RegisteredLookup Extraction`
`JSON`示例如下：
```
"extractionFn":{
    "type":"registeredLookup",
    "lookup":<lookup_string>,
    "retainMissingValue":<false | true>,   
    "replaceMissingValueWith":<replace_string>,  
    "injective":<false | true>,  
    "optimize":<false | true>, 
}
```

### <a id="Extraction-SubString" href="Extraction-SubString"></a> 9. `SubString Extraction`
`SubString Extraction`返回从提供的索引开始至所需长度的子字符串。`JSON`示例如下：
```
"extractionFn":{
    "type":"substring",
    "index":10,
    "length":20
}
```
### <a id="Extraction-Cascade" href="Extraction-Cascade"></a> 10. `Cascade Extraction`
`Cascade Extraction`按指定的顺序将指定的提取函数转换为维度值。`JSON`示例如下：
```
"extractionFn":{
    "type":"cascade",
    "extractionFns":[{<extraction>},{<extraction>}]
}
```
### <a id="Extraction-StringFormat" href="Extraction-StringFormat"></a> 11. `StringFormat Extraction`
`StringFormat Extraction`返回根据给定的格式字符串格式化的维度值。`JSON`示例如下：
```
"extractionFn":{
    "type":"stringFormat",
    "format":<format_string>,
    "nullHandling":{<nullHandling>}  
}
```
### <a id="Extraction-Upper" href="Extraction-Upper"></a> 12. `Upper Extraction`
 `Upper Extraction`返回大写的维度值。`JSON`示例如下：
```
"extractionFn":{
    "type":"upper",
    "locale":<locale_string>
}
```
### <a id="Extraction-Lower" href="Extraction-Lower"></a> 13. `Lower Extraction`
`Lower Extraction`返回小写的维度值。`JSON`示例如下：
```
"extractionFn":{
    "type":"lower",
    "locale":<locale_string>
}
```


## <a id="aggregation" href="aggregation"></a> aggregation 聚合

- `Aggregation` 类别详情如下：
  - [`Count`](#Aggregation-Count)
  - [`Cardinality`](#Aggregation-Cardinality)
  - [`HyperUnique`](#Aggregation-HyperUnique)
  - [`DoubleMax`](#Aggregation-DoubleMax)
  - [`DoubleMin`](#Aggregation-DoubleMin)
  - [`DoubleSum`](#Aggregation-DoubleSum)
  - [`LongMax`](#Aggregation-LongMax)
  - [`LongMin`](#Aggregation-LongMin)
  - [`LongSum`](#Aggregation-LongSum)
  - [`Javascript`](#Aggregation-Javascript)
  - [`DateMin`](#Aggregation-DateMin)
  - [`DateMax`](#Aggregation-DateMax)
  - [`Filtered`](#Aggregation-Filtered)
  - [`ThetaSketch`](#Aggregation-ThetaSketch)

### <a id="Aggregation-Count" href="Aggregation-Count"></a>1. `Count Aggregation`
用于计算Druid的数据行数，相当于`count()`。`Count Aggregation`的JSON示例如下：
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

### <a id="Aggregation-Cardinality" href="Aggregation-Cardinality"></a> 2. `Cardinality Aggregation(已废弃)`
在查询时，`Cardinality Aggregation`使用`HyperLogLog`算法计算给定维度集合的基数，相当于`distinct()`。`Cardinality Aggregation` 的`JSON`示例如下：
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
当设置`byRow`为`false`（默认值）时，它计算由所有给定维度的所有维度值的并集组成的集合的基数。

### <a id="Aggregation-HyperUnique" href="Aggregation-HyperUnique"></a> 3. `HyperUnique Aggregation`
在查询时，`HyperUnique Aggregation` 使用`HyperLogLog`算法计算给定维度集合的基数。`JSON`示例如下：

```
"aggregations":[
    {
        "type":"lucene_hyperUnique",
        "name":<name_string>,
        "fieldName":<fieldName_string>
    }
]
```
使用示例如下:
```
"aggregations":[
    {
        "type":"lucene_hyperUnique",
        "name":"ageCount",
        "fieldName":"age"
    }
]
```

查询结果如下:
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




### <a id="Aggregation-DoubleMax" href="Aggregation-DoubleMax"></a> 4. `DoubleMax Aggregation`
结果的最大值，该值类型为 `double` ，维度的类型支持 `int`,`long`,`float`,相当于`max(<fieldName_string>)`。`JSON`示例如下：
```
"aggregations":[
    {
        "type":"lucene_doubleMax",
        "name":<name_string>,
        "fieldName":<fieldName_string>
    }
]
```
- `name`- 结果输出的名称 
- `fieldName`- 维度的名称

使用示例如下:
```
"aggregations": [
    {
        "type":"lucene_doubleMax",
        "name":"max",
        "fieldName":"age"
    }
]
```
返回结果如下:
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

### <a id="Aggregation-DoubleMin" href="Aggregation-DoubleMin"></a> 5. `DoubleMin Aggregation`
结果的最小值，该值类型为`double`，输入的值类型为`int`,`long`,`float`,相当于`min(<fieldName_string>)`。`JSON`示例如下：
```
"aggregations": [
    {
        "type":"lucene_doubleMin",
        "name":<name_string>,
        "fieldName":<fieldName_string>
    }
]
```
- `name`- 结果输出的名称 
- `fieldName`- 求最小值的列的名称

###  <a id="Aggregation-DoubleSum" href="Aggregation-DoubleSum"></a> 6. `DoubleSum Aggregation`
将查询到的值的和计算为`double`类型的数，输入的值类型为`int`,`long`,`float`,相当于`sum(<fieldName_string>)`。`JSON`示例如下：
```
"aggregations": [
    {
        "type":"lucene_doubleSum",
        "name":<name_string>,
        "fieldName":<fieldName_string> 
    }
]
```
- `name`- 求和值的输出名称 
- `fieldName`- 维度的名称

### <a id="Aggregation-LongMax" href="Aggregation-LongMax"></a> 7. `LongMax Aggregation`
结果的最大值，该值类型为64位有符号整数，输入的值类型为`int`,`long`，相当于`max(<fieldName_string>)`。`JSON`示例如下：
```
"aggregations": [
    {
        "type":"lucene_longMax",
        "name":<name_string>,
        "fieldName":<fieldName_string>
    }
]
```
- `name`- 结果输出的名称 
- `fieldName`- 维度的名称


### <a id="Aggregation-LongMin" href="Aggregation-LongMin"></a> 8. `LongMin Aggregation`
结果的最小值，该值类型为64位有符号整数，输入的值类型为`int`,`long`，相当于`min(<fieldName_string>)`。`JSON`示例如下：
```
"aggregations": [
    {
        "type":"lucene_longMin",
        "name":<name_string>,
        "fieldName":<fieldName_string>
    }
]
```
- `name`- 结果输出的名称 
- `fieldName`- 维度的名称

### <a id="Aggregation-LongSum" href="Aggregation-LongSum"></a> 9. `LongSum Aggregation`
结果的的和，该值类型为64位有符号整数，输入的值类型为`int`,`long`，相当于`sum(<fieldName_string>)` 。`JSON`示例如下：
```
"aggregations": [
    {
        "type":"lucene_longSum",
        "name":<name_string>,
        "fieldName":<fieldName_string> 
    }
]
```
- `name`- 结果输出的名称 
- `fieldName`- 维度的名称


### <a id="Aggregation-Javascript" href="Aggregation-Javascript"></a> 10. `Javascript Aggregation`

如果上述聚合器无法满足需求，`Druid`还提供了`JavaScript Aggregation`。用户可以自己写`JavaScript function`，其中指定的列即为`function`的入参。`JavaScript Aggregation` 的`JSON`示例如下：

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
- `name`:这组`JavaScript`函数的名称
- `fieldNames`:参数的名字  

使用示例如下:
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

### <a id="Aggregation-DateMin" href="Aggregation-DateMin"></a> 11. `DateMin Aggregation`

结果的最小值，该值类型为`date`, 输入的值的类型必须是`date`。`DateMin Aggregation`的`JSON`示例如下：
```
"aggregations": [
    {
        "type":"lucene_dateMin",
        "name":"<name_string>",
        "fieldName":"<fieldName_string>"
    }
]
```
使用示例如下:
```
"aggregations": [
    {
        "type":"lucene_dateMin",
        "name":"minDate",
        "fieldName":"birthday"
    }
]
```
查询结果如下:
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



### <a id="Aggregation-DateMax" href="Aggregation-DateMax"></a> 12. `DateMax Aggregation`
结果的最小值，该值类型为`date`,输入的值的类型必须是`date`。`DateMax Aggregation` 的`JSON`示例如下：
```
"aggregations": [
    {
        "type":"lucene_dateMax",
        "name":"<name_string>",
        "fieldName":"<fieldName_string>"
    }
]
```
### <a id="Aggregation-Filtered" href="Aggregation-Filtered"></a>13. `Filtered Aggregation`

`Filtered Aggregation`可以在`aggregation`中指定`Filter`规则。只对满足规则的维度进行聚合，以提升聚合效率。`JSON`示例如下：
```
"aggregations": [
    {
        "type":"lucene_filtered",
        "aggregator":<aggregator>, 
        "filter":"<filter>
    }
]
```
使用示例如下:
```
"aggregations":[
    {
        "type":"lucene_filtered",
        "aggregator": {
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

该聚合只对`age>20`的记录实行。

查询结果如下:
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

### <a id="Aggregation-ThetaSketch" href="Aggregation-ThetaSketch"></a> 14. `ThetaSketch Aggregation`
`ThetaSketch Aggregation`的`JSON`示例如下：
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
`PostAggregation`可以对`Aggregation`的结果进行二次加工并输出。最终的输出既包含`Aggregation`的结果，也包含`PostAggregation`的结果。使用`PostAggregation`必须包含`Aggregation`。
- `PostAggregation` 类别详情如下：
  - [`Arithmetic`](#PostAggregation-Arithmetic)
  - [`FieldAccess`](#PostAggregation-FieldAccess)
  - [`Constant`](#PostAggregation-Constant)
  - [`HyperUniqueCardinality`](#PostAggregation-HyperUniqueCardinality)
  - [`DataSketch`](#PostAggregation-DataSketch)
  - [`Buckets`](#PostAggregation-Buckets)
  - [`CustomBuckets`](#PostAggregation-CustomBuckets)
  - [`EqualBuckets`](#PostAggregation-EqualBuckets)
  - [`Javascript`](#PostAggregation-Javascript)
  - [`Max`](#PostAggregation-Max)
  - [`Min`](#PostAggregation-Min)

### <a id="PostAggregation-Arithmetic" href="PostAggregation-Arithmetic"></a> 1. `Arithmetic PostAggregation`
`Arithmetic PostAggregation`支持对`Aggregation`的结果和其他`Arithmetic PostAggregation`的结果进行“ + ”，“ - ”，“ * ”，“ / ”和“ quotient ”计算，`quotient`划分的行为像常规小数点的划分。
  
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
- `“quotient”`不判断分母是否为0。
- 当`Arithmetic PostAggregation`的结果参与排序时，默认使用`float`类型。用户可以手动通过`Ordering`字段指定排序方式。

使用示例如下:
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
以上示例可以计算最大年龄和最小年龄之间的年龄差。

### <a id="PostAggregation-FieldAccess" href="PostAggregation-FieldAccess"></a> 2. `FieldAccess PostAggregation`
`FieldAccess PostAggregation`返回指定的`Aggregation`的值，在`PostAggregation`中大部分情况下使用`fieldAccess`来访问`Aggregation`。在`fieldName`中指定`Aggregation`里定义的`name`，如果对`HyperUnique`的结果进行访问，则需要使用`hyperUniqueCardinality`。`FieldAccess PostAggregation`的`JSON`示例如下：
```
"postAggregations":[
    {
        "type":"fieldAccess",
        "name":<output_name>,
        "fieldName":<aggregator_name>
    }
]
```

使用示例如下:
```
"postAggregations":[
    {
        "type":"fieldAccess",
        "name":"field",
        "fieldName":"__VALUE__"
    }
]
```
结果如下:
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


### <a id="PostAggregation-Constant" href="PostAggregation-Constant"></a> 3. `Constant PostAggregation`
`Constant PostAggregation`会多返回一个常数，比如100。可以将`Aggregation`返回的结果转换为百分比。`JSON`示例如下：
```
"postAggregations":[
    {
        "type":"constant",
        "name":<output_name>,
        "value":<numerical_value>
    }
]
```
使用示例如下:
```
"postAggregations":[
    {
        "type":"constant",
        "name":"num",
        "value":10
    }
]
```
结果如下:
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

### <a id="PostAggregation-HyperUniqueCardinality" href="PostAggregation-HyperUniqueCardinality"></a>4. `HyperUniqueCardinality PostAggregation`
`HyperUniqueCardinality PostAggregation`得到`HyperUnique Aggregation`的结果，使之参与到`PostAggregation`的计算中。`JSON`示例如下：  
```
"postAggregations":[
    {
        "type":"lucene_hyperUniqueCardinality",
        "name":<output name>,
        "fieldName":<the name field value of the hyperUnique aggregator>
    }
]
```

### <a id="PostAggregation-DataSketch" href="PostAggregation-DataSketch"></a>5. `DataSketch PostAggregation`

`Druid DataSketch`是基于`Yahoo`开源的`Sketch`包实现的数据近似计算功能。    
#### 5.1 `SketchEstimate PostAggregation`
`SketchEstimate PostAggregation`用于计算`Sketch`的估计值，`JSON`示例如下：
```
"postAggregations":[
    {
        "type":"lucene_sketchEstimate",
        "name":"<name_string>",
        "field":{<postAggregator>}
    }
]
```

#### 5.2 `SketchSetOper PostAggregation`
`SketchSetOper PostAggregation`用于`Sketch`的集合运算，`JSON`示例如下：
```
"postAggregations":[
    {
        "type":"lucene_sketchSetOper",
        "name":"<name_string>",
        "func":"<func_string>",
        "size":20,
        "fields":[<postAggregator>,<postAggregator>,...] 
    }
]
```

### <a id="PostAggregation-Buckets" href="PostAggregation-Buckets"></a> 6. `Buckets PostAggregation`
`Buckets PostAggregation`的`JSON`示例如下：
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
- `bucketSize`: `bucket`的大小
- `offset`: `bucket`的偏移量


### <a id="PostAggregation-CustomBuckets" href="PostAggregation-CustomBuckets"></a>7. `CustomBuckets PostAggregation`
`CustomBuckets PostAggregation`的`JSON`示例如下：
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

### <a id="PostAggregation-EqualBuckets" href="PostAggregation-EqualBuckets"></a> 8. `EqualBuckets PostAggregation`
`EqualBuckets PostAggregation`的`JSON`示例如下：：
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





### <a id="PostAggregation-Javascript" href="PostAggregation-Javascript"></a>9. `Javascript PostAggregation`
`Javascript PostAggregation`将提供的`JavaScript`函数应用于给定字段，`JSON`示例如下：
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

使用示例如下:
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


### <a id="PostAggregation-Max" href="PostAggregation-Max"></a>10. `Max PostAggregation`
`Max PostAggregation`用于计算最大值，`JSON`示例如下：
```
"postAggregations":[
    {
        "type":"max",
        "name":"<output_name>",
        "fieldName":"<post_aggregator>" 
    }
]
```

### <a id="PostAggregation-Min" href="PostAggregation-Min"></a>11. `Min PostAggregation`
`Min PostAggregation`用于计算最小值，`JSON`示例如下：
```
"postAggregations":[
    {
        "type":"min",
        "name":"<output_name>",
        "fieldName":"<post_aggregator>" 
    }
]
```


## <a id="having" href="having"></a> Having

类似于`SQL`中的`having`操作，对`GroupBy`的结果进行筛选。
- `having` 类别详情如下：
  - [`And`](#Having-And)
  - [`Or`](#Having-Or)
  - [`Not`](#Having-Not)
  - [`EqualTo`](#Having-EqualTo)
  - [`GreaterThan`](#Having-GreaterThan)
  - [`LessThan`](#Having-LessThan)
  - [`DimSelector`](#Having-DimSelector)
  - [`Always`](#Having-Always)  
  
### 1. 逻辑表达式过滤器
#### <a id="Having-And" href="Having-And"></a>1.1 `And`
和，`JSON`示例如下：
```
{
    "type":"and",
    "havingSpecs":[<havingSpec>,<havingSpec>,..]
}
```

#### <a id="Having-Or" href="Having-Or"></a>1.2 `Or`
或，`JSON`示例如下：
```
{
    "type":"or",
    "havingSpecs":[<havingSpec>,<havingSpec>,..]
}
```

#### <a id="Having-Not" href="Having-Not"></a>1.3 `Not`
非，`JSON`示例如下：
```
{
    "type":"not",
    "havingSpecs":{<havingSpec>}
}
```
### 2. 数值过滤器

#### <a id="Having-EqualTo" href="Having-EqualTo"></a>2.1 `EqualTo`
等于，`JSON`示例如下：
```
{
    "type":"equalTo",
    "aggregation":"aggName",
    "value":10
}
```

#### <a id="Having-GreaterThan" href="Having-GreaterThan"></a>2.2 `GreaterThan`
大于，`JSON`示例如下：
```
{
    "type":"greaterThan",
    "aggregation":"aggName",
    "value":10
}
```

#### <a id="Having-LessThan" href="Having-LessThan"></a>2.3 `LessThan`
小于，`JSON`示例如下：
```
{
    "type":"lessThan",
    "aggregation":"aggName",
    "value":10
}
```

### <a id="Having-DimSelector" href="Having-DimSelector"></a>3. `DimSelector`
`DimSelector`将匹配尺寸值等于指定值的行，`JSON`示例如下：
```
{
    "type":"dimSelector",
    "dimension":"dimName",
    "value":<value_string>,
    "extractionFn":{<extractionFn>}
}
```
### <a id="Having-Always" href="Having-Always"></a>4. `Always`
总是，即不进行筛选，全部返回，`JSON`示例如下：
```
{
    "type":"always",
}
```