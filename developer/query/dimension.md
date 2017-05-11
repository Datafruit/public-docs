# Tindex-Query-Json `dimension`属性详情如下

## 维度
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
