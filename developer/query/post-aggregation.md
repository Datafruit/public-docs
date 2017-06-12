# Tindex-Query-Json `post-aggregation`属性详情如下

## `PostAggregation`
`PostAggregation`可以对`Aggregation`的结果进行二次加工并输出。最终的输出既包含`Aggregation`的结果，也包含`PostAggregation`的结果。使用`PostAggregation`必须包含`Aggregation`。
- `PostAggregation` 类别详情如下：
  - [`Arithmetic`](#PostAggregation-Arithmetic)
  - [`FieldAccess`](#PostAggregation-FieldAccess)
  - [`Constant`](#PostAggregation-Constant)
  - [`HyperUniqueCardinality`](#PostAggregation-HyperUniqueCardinality)
  - [`DataSketch`](#PostAggregation-DataSketch)
  - [`Buckets`](#PostAggregation-Buckets)
  - [`CustomBuckets`](#PostAggregation-CustomBuckets)
  - [`EqualBuckets`](#PostAggregation-EqualBuckets)
  - [`Javascript`](#PostAggregation-Javascript)
  - [`Max`](#PostAggregation-Max)
  - [`Min`](#PostAggregation-Min)

### <a id="PostAggregation-Arithmetic" href="PostAggregation-Arithmetic"></a> 1. `Arithmetic PostAggregation`
`Arithmetic PostAggregation`支持对`Aggregation`的结果和其他`Arithmetic PostAggregation`的结果进行“ + ”，“ - ”，“ * ”，“ / ”和“ quotient ”计算，`quotient`划分的行为像常规小数点的划分。
  
```
"postAggregations":{
    "type":"arithmetic",
    "name":<name_string>,
    "fn":<fnName_string>,
    "fields":[<postAggregator>,<postAggregator>...],
    "ordering":<ordering_string>
}
```
- 对于“/”，如果分母为0，则返回0。
- `“quotient”`不判断分母是否为0。
- 当`Arithmetic PostAggregation`的结果参与排序时，默认使用`float`类型。用户可以手动通过`Ordering`字段指定排序方式。

使用示例如下:
```
"postAggregations":[
  {
    "type":"arithmetic",
    "name":"age",
    "fn":"-",
    "fields":[
      {
        "type":"hyperUniqueCardinality",
        "fieldName":"max(age)"
      },
      {
        "type":"hyperUniqueCardinality",
        "fieldName":"min(age)" 
      }
    ],
    "ordering":<ordering_string>
  }
]
```
以上示例可以计算最大年龄和最小年龄之间的年龄差。

### <a id="PostAggregation-FieldAccess" href="PostAggregation-FieldAccess"></a> 2. `FieldAccess PostAggregation`
`FieldAccess PostAggregation`返回指定的`Aggregation`的值，在`PostAggregation`中大部分情况下使用`fieldAccess`来访问`Aggregation`。在`fieldName`中指定`Aggregation`里定义的`name`，如果对`HyperUnique`的结果进行访问，则需要使用`hyperUniqueCardinality`。`FieldAccess PostAggregation`的`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"fieldAccess",
    "name":<output_name>,
    "fieldName":<aggregator_name>
  }
]
```

使用示例如下:
```
"postAggregations":[
  {
    "type":"fieldAccess",
    "name":"field",
    "fieldName":"__VALUE__"
  }
]
```
结果如下:
```
[
  {
    "timestamp": "2017-01-01T00:00:00.000Z",
    "result": {
      "field": 100000,
      "__VALUE__": 100000
    }
  }
]
```


### <a id="PostAggregation-Constant" href="PostAggregation-Constant"></a> 3. `Constant PostAggregation`
`Constant PostAggregation`会多返回一个常数，比如100。可以将`Aggregation`返回的结果转换为百分比。`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"constant",
    "name":<output_name>,
    "value":<numerical_value>
  }
]
```
使用示例如下:
```
"postAggregations":[
  {
    "type":"constant",
    "name":"num",
    "value":10
  }
]
```
结果如下:
```
[
  {
    "timestamp": "2017-01-01T00:00:00.000Z",
    "result": {
      "num": 10,
      "__VALUE__": 100000
    }
  }
]
```

### <a id="PostAggregation-HyperUniqueCardinality" href="PostAggregation-HyperUniqueCardinality"></a>4. `HyperUniqueCardinality PostAggregation`
`HyperUniqueCardinality PostAggregation`得到`HyperUnique Aggregation`的结果，使之参与到`PostAggregation`的计算中。`JSON`示例如下：  
```
"postAggregations":[
  {
    "type":"lucene_hyperUniqueCardinality",
    "name":<output name>,
    "fieldName":<the name field value of the hyperUnique aggregator>
  }
]
```


### <a id="PostAggregation-DataSketch" href="PostAggregation-DataSketch"></a>5. `DataSketch PostAggregation`

`Druid DataSketch`是基于`Yahoo`开源的`Sketch`包实现的数据近似计算功能。    
#### 5.1 `SketchEstimate PostAggregation`
`SketchEstimate PostAggregation`用于计算`Sketch`的估计值，`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"lucene_sketchEstimate",
    "name":"<name_string>",
    "field":{<postAggregator>}
  }
]
```

#### 5.2 `SketchSetOper PostAggregation`
`SketchSetOper PostAggregation`用于`Sketch`的集合运算，`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"lucene_sketchSetOper",
    "name":"<name_string>",
    "func":"<func_string>",
    "size":20,
    "fields":[<postAggregator>,<postAggregator>,...] 
  }
]
```

### <a id="PostAggregation-Buckets" href="PostAggregation-Buckets"></a> 6. `Buckets PostAggregation`
`Buckets PostAggregation`的`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"buckets",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>",
    "bucketSize":4.5,
    "offset":3.2
  }
]
```
- `bucketSize`: `bucket`的大小
- `offset`: `bucket`的偏移量


### <a id="PostAggregation-CustomBuckets" href="PostAggregation-CustomBuckets"></a>7. `CustomBuckets PostAggregation`
`CustomBuckets PostAggregation`的`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"customBuckets",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>",
    "breaks":[1.2,3.5]
  }
]
```

### <a id="PostAggregation-EqualBuckets" href="PostAggregation-EqualBuckets"></a> 8. `EqualBuckets PostAggregation`
`EqualBuckets PostAggregation`的`JSON`示例如下：：
```
"postAggregations":[
  {
    "type":"equalBuckets",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>",
    "numBuckets":20
  }
]
```  





### <a id="PostAggregation-Javascript" href="PostAggregation-Javascript"></a>9. `Javascript PostAggregation`
`Javascript PostAggregation`将提供的`JavaScript`函数应用于给定字段，`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"javascript",
    "name":"<output_name>",
    "fieldNames":["<aggregator_name>","<aggregator_name>",...],	
    "function":"<javascript function>"  
  }
]
```

使用示例如下:
```
"postAggregations":[
  {
    "type": "javascript",
    "name": "absPercent",
    "fieldNames": ["delta", "total"],
    "function": "function(delta, total) { return 100 * Math.abs(delta) / total; }"
  }
]
```


### <a id="PostAggregation-Max" href="PostAggregation-Max"></a>10. `Max PostAggregation`
`Max PostAggregation`用于计算最大值，`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"max",
    "name":"<output_name>",
    "fieldName":"<post_aggregator>" 
 }
]
```

### <a id="PostAggregation-Min" href="PostAggregation-Min"></a>11. `Min PostAggregation`
`Min PostAggregation`用于计算最小值，`JSON`示例如下：
```
"postAggregations":[
  {
    "type":"min",
    "name":"<output_name>",
    "fieldName":"<post_aggregator>" 
  }
]
```


