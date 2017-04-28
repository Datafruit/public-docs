## 聚合
聚合函数是查询规范的一部分，可以在数据进入druid之前对其进行总结处理。

aggregations.type 可选项：lucene_cardinality ， lucene_count ，lucene_doubleMax ，lucene_doubleMin ，lucene_doubleSum ， lucene_hyperUnique ， lucene_javascript ， lucene_longMax ， lucene_longMin , lucene_longSum

### 基数
aggregations.type=lucene_cardinality 时，参数：
```
{
    "type":"lucene_cardinality",
    "name":"name_string",
    "fieldNames":[
    	"<fieldName_string>","<fieldName_string>",...
    ], 
    "byRow":true 
}
```
计算一组druid维度的基数,相当于distinct()。  

当设置byRow为false（默认值）时，它计算由所有给定维度的所有维度值的并集组成  
的集合的基数。

### 计数
aggregations.type=lucene_count 时，参数：
```
{
    "type":"lucene_count",
    "name":"<name_string>",
}
```
可以计算行的数量，相当于`count()`

### 最大值（double）
aggregations.type=lucene_doubleMax 时，参数： 
```
{
    "type":"lucene_doubleMax",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
}
```
求查询到的值中的最大值，该值类型为double，相当于`max("<fieldName_string>")`

### 最小值（double）
aggregations.type=lucene_doubleMin 时，参数：
```
{
    "type":"lucene_doubleMin",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>" 
}
```
求查询到的值中的最小值，该值类型为double，相当于`max("<fieldName_string>")`

### 总和（double）
aggregations.type=lucene_doubleSum 时，参数：
```
{
    "type":"lucene_doubleSum",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>" 
}
```
将查询到的值的和计算为double类型的数，相当于`sum("<fieldName_string>")`

### hyperUnique
aggregations.type=lucene_hyperUnique 时，参数：
```
{
    "type":"lucene_hyperUnique",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
}
```

### javascript

aggregations.type=lucene_javascript 时，参数：
```
{
    "type":"lucene_javascript",
    "name":"<name_string>",
    "fieldNames":[
    	"<fieldName_string>","<fieldName_string>"
    ], 
    "fnAggregate":"<fnAggregate_string>", 
    "fnReset":"<fnReset_string>", 
    "fnCombine":"<fnCombine_string>" 
}
```
计算一组任意JavaScript函数（允许使用度量和维度）。 
- name:这组JavaScript函数的名称
- fieldNames:参数的名字  

 
**example**
```
{
  "type": "lucene_javascript",
  "name": "sum(log(x)*y) + 10",
  "fieldNames": ["x", "y"],
  "fnAggregate" : "function(current, a, b)      { return current + (Math.log(a) * b); }",
  "fnCombine"   : "function(partialA, partialB) { return partialA + partialB; }",
  "fnReset"     : "function()                   { return 10; }"
}
```
### 最大值（long）
aggregations.type=lucene_longMax 时，参数：
```
{
    "type":"lucene_longMax",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>" 
}
```
求查询到的值中的最小值，该值类型为64位有符号整数，相当于`max("<fieldName_string>")`   

- name- 求和值的输出名称 
- fieldName- 求总和的列的名称


### 最小值（long）
aggregations.type=lucene_longMin 时，参数：
```
{
    "type":"lucene_longMin",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
}
```
求查询到的值中的最小值，该值类型为64位有符号整数，相当于`min("<fieldName_string>")`   

- name- 求和值的输出名称 
- fieldName- 求总和的列的名称

### 总和（long）
aggregations.type=lucene_longSum 时，参数：
```
{
    "type":"lucene_longSum",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>" 
}
```
将查询到的值的和计算为64位有符号整数，相当于`sum("<fieldName_string>")`   

- name- 求和值的输出名称 
- fieldName- 求总和的列的名称
