# Tindex-Query-Json `extraction-fn`属性详情如下

## 提取过滤器

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
