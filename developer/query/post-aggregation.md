# Tindex-Query-Json `post-aggregation`属性详情如下

##  postAggregation聚合
postAggregations.type 可选项: `arithmetic` , `buckets`, `constant` , `customBuckets` ,  `equalBuckets` ,  `fieldAccess` , `hyperUniqueCardinality` , `javascript` , `max` , `min` , `sketchEstimate` , `sketchSetOper`

### 算术

postAggregations.type=arithmetic 时，参数：  
```
{
    "type":"arithmetic",
    "name":"<name_string>",
    "fn":"<fnName_string>",
    "fields":[
    		<postAggregator>,<postAggregator>,...
    ],
    "ordering":"ordering_string"
}
```
算术后聚合器将提供的函数从左到右应用于给定的字段。字段可以是聚合器或其他后期聚合器。

支持的功能有+，-，*，/，和quotient。quotient划分的行为像常规小数点的划分。
- ordering:定义了排序的顺序。

### buckets
postAggregations.type=buckets 时，参数：
```
{
    "type":"buckets",
    "name":"<output_name>",
    "fieldName":"<aggregator_name>",
    "bucketSize":4.5,
    "offset":3.2
}
```
装到给定size相同的bucket里面
- bucketSize:bucket的大小
- offset:抵消

### 常量

postAggregations.type=constant 时，参数：
```
{
    "type":"constant",
    "name":"<output_name>",
    "value":<numerical_value>
}
```
总是返回指定的值。

### 自定义buckets
postAggregations.type=customBuckets 时，参数：
```
{
    "type":"customBuckets",
    "name":"<output_name>",
    "fieldName":"<aggregator_name>",
    "breaks":[
    	1.2,
    	3.5
    ]
}
```
- breaks：bucket的分界点  
装到自定义的bucket里面

### equalBuckets
postAggregations.type=equalBuckets 时，参数：
```
{
    "type":"equalBuckets",
    "name":"<output_name>",
    "fieldName":"<aggregator_name>",
    "numBuckets":20
}
```  


### fieldAccess
postAggregations.type=fieldAccess 时，参数：
```
{
    "type":"fieldAccess",
    "name":"<output_name>",
    "fieldName":"<aggregator_name>"
}
```
返回指定聚合器生成的值。
### hyperUniqueCardinality

postAggregations.type=hyperUniqueCardinality 时，参数：
```
{
    "type":"hyperUniqueCardinality",
    "name":"<output name>",
    "fieldName":"<the name field value of the hyperUnique aggregator>" 
}
```
用于包装hyperUnique对象，以便可以在后期聚合中使用。  

**example**
```
  "aggregations" : [{
    {"type" : "count", "name" : "rows"},
    {"type" : "hyperUnique", "name" : "unique_users", "fieldName" : "uniques"}
}],
"postAggregations" : [{
    "type"   : "arithmetic",
    "name"   : "average_users_per_row",
    "fn"     : "/",
    "fields" : [
      { "type" : "hyperUniqueCardinality", "fieldName" : "unique_users" },
      { "type" : "fieldAccess", "name" : "rows", "fieldName" : "rows" }
    ]
}]
```


### javascript
postAggregations.type=javascript 时，参数：
```
{
    "type":"javascript",
    "name":"<output_name>",
    "fieldNames":[
    	"<aggregator_name>","<aggregator_name>",...
    ],	
    "function":"<javascript function>"  
}
```
将提供的JavaScript函数应用于给定字段。     

**example**
```
{
  "type": "javascript",
  "name": "absPercent",
  "fieldNames": ["delta", "total"],
  "function": "function(delta, total) { return 100 * Math.abs(delta) / total; }"
}
```


### 最大
postAggregations.type=max 时，参数：
```
{
    "type":"max",
    "name":"<output_name>",
    "fieldName":"<post_aggregator>" 
}
```
计算最大值
### 最小
postAggregations.type=min 时，参数：
```
{
    "type":"min",
    "name":"<output_name>",
    "fieldName":"<post_aggregator>" 
}
```
计算最小值

### sketchEstimate
postAggregations.type=sketchEstimate 时，参数：
```
{
    "type":"sketchEstimate",
    "name":"<name_string>",
    "field":{
    	<postAggregator>
    }
}
```
将堆外内存的内容读取出来

### sketchSetOper
postAggregations.type=sketchSetOper 时，参数：
```
{
    "type":"sketchSetOper",
    "name":"<name_string>",
    "func":"<func_string>",
    "size":20,
    "fields":[
        <postAggregator>,<postAggregator>,...
    ] 
}
```
将多个堆外内存的内容进行操作
