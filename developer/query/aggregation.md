# Tindex-Query-Json `aggregation`属性详情如下

## `Aggregation` 聚合
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

### <a id="Aggregation-Cardinality" href="Aggregation-Cardinality"></a> 2. `Cardinality Aggregator(已废弃)`
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

### <a id="Aggregation-HyperUnique" href="Aggregation-HyperUnique"></a> 3. `HyperUnique Aggregator`
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
    "aggregator":  {
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