# Tindex-Query-Json `filter`属性详情如下

## Filter 过滤器

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

### 12. Extraction Filter

&#160; &#160; &#160; &#160;Extraction Filter使用一些特定的提取函数匹配维度。  
&#160; &#160; &#160; &#160;extraction类型可选项：time , regex , partial , searchQuery , javascript , timeFormat , identity , lookup , registeredLookup , substring , cascade , stringFormat , upper , lower 。  
&#160; &#160; &#160;
&#160;详见[`extraction-fn`](/developer/query/extraction-fn.md)。