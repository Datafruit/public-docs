
# parser
parser 转换支持的类型有： json ， csv ， tsv ， jsonLowercase ， timeAndDims ， regex ， javascript


## 1. json
```
"parseSpec":{
  "format":"json",
  "timestampSpec":TimestampSpec timestampSpec,
  "dimensionsSpec":DimensionsSpec dimensionsSpec,
  "flattenSpec":{
    "useFieldDiscovery": Boolean useFieldDiscovery,
    "fields": List<JSONPathFieldSpec> fields
  }
  "featureSpec":Map<String, Boolean> featureSpec
}
```
- **parseSpec.timestampSpec:** 时间戳列以及事件的格式，详见[`timestampSpec`](#timestampSpec)
- **parseSpec.dimensionsSpec:** 包含如何解析数据的相关内容，详见[`dimensionsSpec`](#dimensionsSpec)
- **parseSpec.flattenSpec:** 包含如何解析json数据的第一层维度说明
- **parseSpec.flattenSpec:** 是否有嵌套维度

**example**
```
"parser":{
    "parseSpec":{
        "format":"json",
        "dimensionsSpec":{
            "dynamicDimension":true,
            "dimensions":[]
        },
        "timestampSpec":{
            "column":"d|place_time",
            "format":"millis"
        }
    },
    "type":"single"
}
```
这个例子用动态维来接入json类型数据源，其中时间戳的维度名为 `d|place_time`，格式为 `millis`。


## 2. csv
```
"parseSpec":{
  "format":"csv",
  "timestampSpec":TimestampSpec timestampSpec,
  "dimensionsSpec":DimensionsSpec dimensionsSpec,
  "listDelimiter"： String listDelimiter,
  "multiValueDelimiter": String multiValueDelimiter,
  "columns": List<String> columns
}
```

- **parseSpec.timestampSpec:** 时间戳列以及事件的格式，详见[`timestampSpec`](#timestampSpec)
- **parseSpec.dimensionsSpec:** 包含如何解析数据的相关内容，详见[`dimensionsSpec`](#dimensionsSpec)
- **parseSpec.listDelimiter:** 行数据分隔符
- **parseSpec.multiValueDelimiter:** 列数据分隔符，用于一个列中有多个值的情形
- **parseSpec.columns:** 对行数据的列进行命名


**example**
```
"parser": {
  "parseSpec": {
    "format": "csv",
    "timestampSpec":{
      "column": "ts",
      "format": "yy-MM-dd HH:mm:ss.SSS"
    },
    "dimensionsSpec": {
      "dimensions": [
        {
          "name": "cardNum",
          "type": "string"
        },
        {
          "name": "level",
          "type": "int"
        },
        {
          "name": "create_time",
          "type": "date", 
          "format":"yy-MM-dd HH:mm:ss.SSS"
        }
      ]
    },
    "listDelimiter": ",",
    "columns": ["ts","cardNum","level","create_time"]
  }
}
```
这个例子接入csv类型数据，这个csv文件有四个维度分别和columns对应。



## 3. tsv
```
"parser"{
  "parseSpec":{
    "format":"tsv",
    "timestampSpec":TimestampSpec timestampSpec,
    "dimensionsSpec":DimensionsSpec dimensionsSpec,
    "delimiter": String delimiter,
    "listDelimiter"： String listDelimiter,
    "columns": List<String> columns
  }
}

```
- **parseSpec.timestampSpec:** 时间戳列以及事件的格式，详见[`timestampSpec`](#timestampSpec)
- **parseSpec.dimensionsSpec:** 包含如何解析数据的相关内容，详见[`dimensionsSpec`](#dimensionsSpec)
- **parseSpec.delimiter:** 行数据分隔符
- **parseSpec.listDelimiter:** 列数据分隔符，用于一个列中有多个值的情形
- **parseSpec.columns:** 对行数据的列进行命名


**example**
```
"parser":{
  "parseSpec": {
      "format": "tsv",
      "timestampSpec": {"column": "myDate","format": "millis"},
      "dimensionsSpec": {
          "dimensions": [
              {"name": "myStr1","type": "string"},
              {"name": "myStr2","type": "string","hasMultipleValues":true }                                  
          ]
      },
      "delimiter":"abc",
      "listDelimiter": "!",
      "columns": ["myDate","myStr1","myStr2"]
  }
}

```
注意这个例子的行数据分隔符为多个字符"abc"，不是常见的单字符分隔符如","   
该例子对应的部分数据为:
```
1484839807587abcqq4hwfhh03abccvv904doqj!632124152
1490986198551abchaig2sabczufi!9733
```
对应的结果为：   
__time | myStr1 | myStr2   
--- | --- | ---
2017-01-19T15:30:07.587Z | qq4hwfhh03 | 632124152, cvv904doqj
2017-03-31T18:49:58.551Z | haig2s | 9733, zufi 

## 4. jsonLowercase
```
"parseSpec":{
  "format":"jsonLowercase",
  "timestampSpec":TimestampSpec timestampSpec,
  "dimensionsSpec":DimensionsSpec dimensionsSpec,
}
```
- **parseSpec.timestampSpec:** 时间戳列以及事件的格式，详见[`timestampSpec`](#timestampSpec)  
- **parseSpec.dimensionsSpec:** 包含如何解析数据的相关内容，详见[`dimensionsSpec`](#dimensionsSpec)

## 5. timeAndDims
```
"parseSpec":{
  "format":"timeAndDims",
  "timestampSpec":TimestampSpec timestampSpec,
  "dimensionsSpec":DimensionsSpec dimensionsSpec,
}
```
- **parseSpec.timestampSpec:** 时间戳列以及事件的格式，详见[`timestampSpec`](#timestampSpec)
- **parseSpec.dimensionsSpec:** 包含如何解析数据的相关内容，详见[`dimensionsSpec`](#dimensionsSpec)

## 6. regex
```
"parseSpec":{
  "format":"regex",
  "timestampSpec":TimestampSpec timestampSpec,
  "dimensionsSpec":DimensionsSpec dimensionsSpec,
  "listDelimiter"： String listDelimiter,
  "columns": List<String> columns,
  
}
```
- **parseSpec.timestampSpec:** 时间戳列以及事件的格式，详见[`timestampSpec`](#timestampSpec)
- **parseSpec.dimensionsSpec:** 包含如何解析数据的相关内容，详见[`dimensionsSpec`](#dimensionsSpec)
- **parseSpec.listDelimiter:** 分隔符
- **parseSpec.columns:** 对行数据的列进行命名

## 7. javascript
```
"parseSpec":{
  "format":"javascript",
  "timestampSpec":TimestampSpec timestampSpec,
  "dimensionsSpec":DimensionsSpec dimensionsSpec,
}
```
- **parseSpec.timestampSpec:** 时间戳列以及事件的格式，详见[`timestampSpec`](#timestampSpec)
- **parseSpec.dimensionsSpec:** 包含如何解析数据的相关内容，详见[`dimensionsSpec`](#dimensionsSpec)


## 参数说明
### <a id="timestampSpec"></a> timestampSpec
时间戳列以及事件的格式
```
timestampSpec:{
  "column": String timestampColumn,
  "format": String format,
  "timeZone": String timeZone,
  "missingValue": DateTime missingValue,
  "reNewTime": Boolean reNewTime
}
```
- **timestampSpec.column:** 时间戳的列名
- **timestampSpec.format:** 时间格式类型：推荐millis
- **timestampSpec.timeZone:** 时区
- **timestampSpec.missingValue:** 时间戳的缺失值
- **timestampSpec.reNewTime:** 是否将时间戳更新为当前时间，默认为false

### <a id="dimensionsSpec"></a> dimensionsSpec
包含如何解析数据的相关内容
```
dimensionsSpec:{
  "dynamicDimension": Boolean dynamicDimension,
  "enableAll": Boolean enableAll,
  "dimensions": List<DimensionSchema> dimensions,
  "dimensionExclusions": List<String> dimensionExclusions,
  "spatialDimensions": List<SpatialDimensionSchema> spatialDimensions
}
```
- **dimensionsSpec.dynamicDimension:** 是否为动态维
- **dimensionsSpec.enableAll:** 是否以json格式列出所有列的值，其列名为"__all"
- **dimensionsSpec.dimensions:** 维度定义列表，对于普通的维度的格式为：{”name":"age","type":"string"}；对于类型为'date'的维度，格式为{”name":"age","type":"date","format":"millis"}；
- **dimensionsSpec.dimensionExclusions:** 不包含的维度
- **dimensionsSpec.spatialDimensions:** 空间维度
