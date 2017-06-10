# Tindex-Query-Json `Having`属性详情如下

## `Having`

&#160; &#160; &#160; &#160;类似于`SQL`中的`having`操作，对`GroupBy`的结果进行筛选。
- `having` 类别详情如下：
  - [`And`](#Having-And)
  - [`Or`](#Having-Or)
  - [`Not`](#Having-Not)
  - [`EqualTo`](#Having-EqualTo)
  - [`GreaterThan`](#Having-GreaterThan)
  - [`LessThan`](#Having-LessThan)
  - [`DimSelector`](#Having-DimSelector)
  - [`Always`](#Having-Always)
### 1. 逻辑表达式过滤器
#### <a id="Having-And" href="Having-And"></a>1.1 `And`
&#160; &#160; &#160; &#160;和，`JSON`示例如下：
```
{
    "type":"and",
    "havingSpecs":[<havingSpec>,<havingSpec>,..]
}
```

#### <a id="Having-Or" href="Having-Or"></a>1.2 `Or`
&#160; &#160; &#160; &#160;或，`JSON`示例如下：
```
{
    "type":"or",
    "havingSpecs":[<havingSpec>,<havingSpec>,..]
}
```

#### <a id="Having-Not" href="Having-Not"></a>1.3 `Not`
&#160; &#160; &#160; &#160;非，`JSON`示例如下：
```
{
    "type":"not",
    "havingSpecs":{<havingSpec>}
}
```
### 2. 数值过滤器

#### <a id="Having-EqualTo" href="Having-EqualTo"></a>2.1 `EqualTo`
&#160; &#160; &#160; &#160;等于，`JSON`示例如下：
```
{
    "type":"equalTo",
    "aggregation":"aggName",
    "value":10
}
```

#### <a id="Having-GreaterThan" href="Having-GreaterThan"></a>2.2 `GreaterThan`
&#160; &#160; &#160; &#160;大于，`JSON`示例如下：
```
{
    "type":"greaterThan",
    "aggregation":"aggName",
    "value":10
}
```

#### <a id="Having-LessThan" href="Having-LessThan"></a>2.3 `LessThan`
&#160; &#160; &#160; &#160;小于，`JSON`示例如下：
```
{
    "type":"lessThan",
    "aggregation":"aggName",
    "value":10
}
```

### <a id="Having-DimSelector" href="Having-DimSelector"></a>3. `DimSelector`
&#160; &#160; &#160; &#160;`DimSelector`将匹配尺寸值等于指定值的行，`JSON`示例如下：
```
{
    "type":"dimSelector",
    "dimension":"dimName",
    "value":<value_string>,
    "extractionFn":{<extractionFn>}
}
```
### <a id="Having-Always" href="Having-Always"></a>4. `Always`
&#160; &#160; &#160; &#160;总是，即不进行筛选，全部返回，`JSON`示例如下：
```
{
    "type":"always",
}
```