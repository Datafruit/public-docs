## having

相当于having语句  

having.type可选项： and , or , not , greaterThan , lessThan , equalTo , dimSelector , always 

### 或
having.type=or 时，参数：
```
{
    "type":"or",
    "havingSpecs":[
    	<havingSpec>,<havingSpec>,..
    ]
}
```
逻辑或
### 非
having.type=not 时，参数：
```
{
    "type":"not",
    "havingSpecs":{
    	<havingSpec>
    }
}
```
逻辑非
### 大于
having.type=greaterThan 时，参数：
```
{
    "type":"greaterThan",
    "aggregation":"aggName",
    "value":10
}
```

### 小于
having.type=lessThan 时，参数：
```
{
    "type":"lessThan",
    "aggregation":"aggName",
    "value":10
}
```
### 等于
having.type=equalTo 时，参数：
```
{
    "type":"equalTo",
    "aggregation":"aggName",
    "value":10
}
```
### dimSelector
having.type=dimSelector 时，参数：
```
{
    "type":"dimSelector",
    "dimension":"dimName",
    "value":"value_string",
    "extractionFn":{
    	<extractionFn>
    }
}
```
### 总是
having.type=always 时，参数：
```
{
    "type":"always",
}
```
