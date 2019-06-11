# Tindex-Query-Json `filter`属性详情如下

## `Filter` 过滤器

`Filter`,即过滤器，在查询语句中是一个`JSON`对象，用来对维度进行筛选，表示维度满足`Filter`的行是我们需要的数据。它基本上等同于`SQL`中的`WHERE`子句。
- `Filter` 类别详情如下：
  - [`Selector`](#Filter-Selector)
  - [`Regex`](#Filter-Regex)
  - [`And`](#Filter-And)
  - [`Or`](#Filter-Or)
  - [`Not`](#Filter-Not)
  - [`Search`](#Filter-Search)
  - [`In`](#Filter-In)
  - [`Bound`](#Filter-Bound)
  - [`JavaScript`](#Filter-JavaScript)
  - [`All`](#Filter-All)
  - [`Lookup`](#Filter-Lookup)
  - [`Lucene`](#Filter-Lucene)
  - [`Extraction`](#Filter-Extraction)
### <a id="Filter-Selector" href="Filter-Selector"></a>1. `Selector Filter`
`Selector Filter`是最简单的过滤器，它将与具体值匹配，功能类似于`SQL`中的`where key=value`，支持提取功能。`Selector Filter`的`JSON`示例如下：
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



### <a id="Filter-All" href="Filter-All"></a> 8. `All Filter`
`All Filter`匹配所有维度值，`JSON`示例如下：
```
{
    "type":"all"
}
```
### <a id="Filter-Lookup" href="Filter-Lookup"></a> 9. `Lookup Filter`
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
### <a id="Filter-Lucene" href="Filter-Lucene"></a>10. `Lucene Filter`
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
### <a id="Filter-Extraction" href="Filter-Extraction"></a> 11. `Extraction Filter`

`Extraction Filter`使用一些特定的提取函数匹配维度。  
`extraction`类型可选项：`time`,`regex`,`partial`,`searchQuery`,`javascript`,`timeFormat`,`identity`,`lookup`,`registeredLookup`,`substring`,`cascade`,`stringFormat`,`upper`,`lower`。  
&#160; &#160; &#160;
&#160;详见[`extraction-fn`](/developer/query/extraction-fn.md)。
