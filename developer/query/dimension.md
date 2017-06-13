# Tindex-Query-Json `dimension`属性详情如下

## `Dimension` 维度
`Dimension` ,即维度。
- `Dimension` 类别详情如下：
  - [`Default`](#Default)
  - [`Extraction`](#Extraction)
  - [`Regex`](#Regex)
  - [`ListFiltered`](#ListFiltered)
  - [`Lookup`](#Lookup)

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

###<a id="Lookup" href="Lookup"></a> 5. `Lookup Dimension`
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