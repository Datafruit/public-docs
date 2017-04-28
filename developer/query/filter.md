## 过滤器
一个过滤器是一个JSON对象，指示在查询的计算中应该包括哪些数据行。它基本上等同于SQL中的WHERE子句。  

filter.type可选项: and , or , not , selector , extraction , regex , search , javascript , spatial , in , bound。


### 选择过滤器
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
选择器过滤器是最简单的过滤器。  

选择器过滤器将与具体值匹配,可用作更复杂过滤器的基本过滤器。  

上面的参数设置这相当于 `WHERE <dimension_string> = '<value_string>'`   。 

支持使用提取功能  。

### 正则表达式过滤器
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
正则表达式过滤器与选择器过滤器类似，但使用正则表达式。它与给定模式匹配指定的维度。  

pattern：给定的模式，可以是任何标准的Java正则表达式。  

支持使用提取功能。

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

JavaScript过滤器将维度与指定的JavaScript谓语函数进行匹配。

支持使用提取功能。


### 搜索过滤器

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
搜索过滤器可用于过滤部分字符串匹配。

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

边界过滤器可用于过滤维度值的范围。它可以用于大于，小于，大于或等于，小于或等于和“之间”（如果同时设置“下”和“上”两者）的比较过滤。

支持提取功能

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
### spatial过滤器
```
filter.type=spatial 时，参数：
{
    "type":"spatial",
    "dimension":"dimension_string",
    "bound":<bound>
}
```
filter.spatial.bound.type可选项： rectangular ，radius

filter.spatial.bound.type=rectangular 时，参数：
```
{
    "type":"rectangular",
    "minCoords":[
    	4.5,
    	5.3
    ],
    "maxCoords":[
    	2.3,
    	5.6
    ],
    "limit":50
}
```
filter.spatial.bound.type=radius 时，参数：
```
{
    "type":"radius",
    "coords":[
    	4.5,
    	5.3
    ],
    "radius":[
    	2.3,
    	5.6
    ],
    "limit":50
}
```

### 提取过滤器

提取过滤器使用一些特定的提取function匹配维度。  

extraction.type可选项：time , regex , partial , searchQuery , javascript , timeFormat , identity , lookup , registeredLookup , substring , cascade , stringFormat , upper , lower 

详见extracionFn

