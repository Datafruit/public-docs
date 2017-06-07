# Tindex-Query-Json `having`属性详情如下

## having

&#160; &#160; &#160; &#160;类似于SQL中的 having 操作，对 GroupBy 的结果进行筛选。支持多种操作：  

### 1. 逻辑表达式过滤器
#### 1.1 And
&#160; &#160; &#160; &#160;和，JSON示例如下：
```
{
    "type":"and",
    "havingSpecs":[<havingSpec>,<havingSpec>,..]
}
```

#### 1.2 Or
&#160; &#160; &#160; &#160;或，JSON示例如下：
```
{
    "type":"or",
    "havingSpecs":[<havingSpec>,<havingSpec>,..]
}
```

#### 1.3 Not
&#160; &#160; &#160; &#160;非，JSON示例如下：
```
{
    "type":"not",
    "havingSpecs":{<havingSpec>}
}
```
### 2. 数值过滤器

#### 2.1 EqualTo
&#160; &#160; &#160; &#160;等于，JSON示例如下：
```
{
    "type":"equalTo",
    "aggregation":"aggName",
    "value":10
}
```

#### 2.2 GreaterThan
&#160; &#160; &#160; &#160;大于，JSON示例如下：
```
{
    "type":"greaterThan",
    "aggregation":"aggName",
    "value":10
}
```

#### 2.3 LessThan 
&#160; &#160; &#160; &#160;小于，JSON示例如下：
```
{
    "type":"lessThan",
    "aggregation":"aggName",
    "value":10
}
```

### 3. DimSelector
&#160; &#160; &#160; &#160;DimSelector 将匹配尺寸值等于指定值的行，JSON示例如下：
```
{
    "type":"dimSelector",
    "dimension":"dimName",
    "value":<value_string>,
    "extractionFn":{<extractionFn>}
}
```
### 4. Always
&#160; &#160; &#160; &#160;总是，即不进行筛选，全部返回，JSON示例如下：
```
{
    "type":"always",
}
```