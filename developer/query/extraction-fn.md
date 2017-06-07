# Tindex-Query-Json `extraction-fn`属性详情如下

## Extraction 提取过滤器

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