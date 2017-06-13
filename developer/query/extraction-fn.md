# Tindex-Query-Json `extraction-fn`属性详情如下

## `Extraction` 提取过滤器

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