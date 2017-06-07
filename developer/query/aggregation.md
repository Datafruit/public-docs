# Tindex-Query-Json `aggregation`属性详情如下

## Aggregation 聚合
&#160; &#160; &#160; &#160;Aggregation，即聚合器。若在摄入阶段就指定，则会在roll up 时就进行计算；当然，也能在查询时指定。聚合器包含以下几种类型。

### 1. Count Aggregation
&#160; &#160; &#160; &#160;用于计算Druid的数据行数，相当于`count()`。Count Aggregation 的JSON示例如下：
```
"aggregations"{
    "type":"lucene_count",
    "name":<name_string>
}
```
### 2. Cardinality Aggregator
&#160; &#160; &#160; &#160;在查询时，Cardinality Aggregation 使用HyperLogLog算法计算给定维度集合的基数，相当于distinct()。Cardinality Aggregation 的JSON示例如下：
```
"aggregations"{
    "type":"lucene_cardinality",
    "name":<name_string>,
    "fieldNames":[<fieldName_string>,<fieldName_string>,...], 
    "byRow":<false | true> 
}
```
&#160; &#160; &#160; &#160;当设置byRow为false（默认值）时，它计算由所有给定维度的所有维度值的并集组成的集合的基数。

### 3. HyperUnique Aggregator
&#160; &#160; &#160; &#160;在查询时，HyperUnique Aggregation 使用HyperLogLog算法计算给定维度集合的基数。HyperUnique Aggregation比Cardinality Aggregation要快得多，因为HyperUnique Aggregation在摄入阶段就会为Metric做聚合，因此在通常情况下，对于单个维度求基数，比较推荐使用 HyperUnique Aggregation。JSON示例如下：

```
"aggregations"{
    "type":"lucene_hyperUnique",
    "name":<name_string>,
    "fieldName":<fieldName_string>
}
```



### 4. DoubleMax Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最大值，该值类型为double，相当于`max(<fieldName_string>)`。JSON示例如下：
```
"aggregations"{
    "type":"lucene_doubleMax",
    "name":<name_string>,
    "fieldName":<fieldName_string>
}
```
- name- 求最大值的输出名称 
- fieldName- 求最大值的列的名称

### 5. DoubleMin Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最小值，该值类型为double，相当于`min(<fieldName_string>)`。JSON示例如下：
```
"aggregations"{
    "type":"lucene_doubleMin",
    "name":<name_string>,
    "fieldName":<fieldName_string>
}
```
- name- 求最小值的输出名称 
- fieldName- 求最小值的列的名称

###  6. DoubleSum Aggregation
&#160; &#160; &#160; &#160;将查询到的值的和计算为double类型的数，相当于`sum(<fieldName_string>)`。JSON示例如下：
```
"aggregations"{
    "type":"lucene_doubleSum",
    "name":<name_string>,
    "fieldName":<fieldName_string> 
}
```
- name- 求和值的输出名称 
- fieldName- 求总和的列的名称

### 7. LongMax Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最大值，该值类型为64位有符号整数，相当于`max(<fieldName_string>)`。JSON示例如下：
```
"aggregations"{
    "type":"lucene_longMax",
    "name":<name_string>,
    "fieldName":<fieldName_string>
}
```
- name- 求最大值的输出名称 
- fieldName- 求最大值的列的名称


### 8. LongMin Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最小值，该值类型为64位有符号整数，相当于`min(<fieldName_string>)`。JSON示例如下：
```
"aggregations"{
    "type":"lucene_longMin",
    "name":<name_string>,
    "fieldName":<fieldName_string>
}
```
- name- 求最小值的输出名称 
- fieldName- 求最小值的列的名称

### 9. LongSum Aggregation
&#160; &#160; &#160; &#160;将查询到的值的和计算为64位有符号整数，相当于`sum(<fieldName_string>)` 。JSON示例如下：
```
"aggregations"{
    "type":"lucene_longSum",
    "name":<name_string>,
    "fieldName":<fieldName_string> 
}
```
- name- 求和值的输出名称 
- fieldName- 求总和的列的名称


### 10. Javascript Aggregation

&#160; &#160; &#160; &#160;如果上述聚合器无法满足需求，Druid还提供了JavaScript Aggregation。用户可以自己写JavaScript function，其中指定的列即为function的入参。JavaScript Aggregation 的JSON示例如下：

```
"aggregations"{
    "type":"lucene_javascript",
    "name":<name_string>,
    "fieldNames":[<fieldName_string>,<fieldName_string>], 
    "fnAggregate":<fnAggregate_string>, 
    "fnReset":<fnReset_string>, 
    "fnCombine":<fnCombine_string> 
}
```
- name:这组JavaScript函数的名称
- fieldNames:参数的名字  

**example**
```
"aggregations"{
    "type": "lucene_javascript",
    "name": "sum(log(x)*y) + 10",
    "fieldNames": ["x", "y"],
    "fnAggregate" : "function(current, a, b)      { return current + (Math.log(a) * b); }",
    "fnCombine"   : "function(partialA, partialB) { return partialA + partialB; }",
    "fnReset"     : "function()                   { return 10; }"
}
```

### 11. DateMin Aggregation

&#160; &#160; &#160; &#160;求查询到的值中的最小值，该值类型为date。DateMin Aggregation 的JSON示例如下：
```
{
    "type":"lucene_dateMin",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
}
```

### 12. DateMax Aggregation
&#160; &#160; &#160; &#160;求查询到的值中的最小值，该值类型为date 。DateMax Aggregation 的JSON示例如下：
```
{
    "type":"lucene_dateMax",
    "name":"<name_string>",
    "fieldName":"<fieldName_string>"
}
```
### 13. Filtered Aggregation

&#160; &#160; &#160; &#160;Filtered Aggregation 可以在aggregation中指定Filter规则。只对满足规则的维度进行聚合，以提升聚合效率。JSON示例如下：
```
{
    "type":"lucene_filtered",
    "aggregator":<aggregator>, 
    "filter":"<filter>
}
```
### 14. ThetaSketch Aggregation
&#160; &#160; &#160; &#160;ThetaSketch Aggregation 的 JSON 示例如下：
```
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
```