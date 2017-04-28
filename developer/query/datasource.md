## 数据源

数据源相当于数据库中的表     

dataSource.type可选项： table , query , union , 也可以是一个字符串()。

###  表数据源
dataSource.type=table 时，参数：    
```
{
    "type":"table",  
    "name":"<string_value>"   
}
```
最常用的数据源，`<string_value>`为源数据源的名称，类似关系数据库中的表名。

### 联合数据源
dataSource.type=union时，参数：
```
{
    "type": "union",
    "dataSources": ["<string_value1>", "<string_value2>", "<string_value3>", ... ]
}
```
该数据源连接两个或多个表数据，`<string_value1>` `<string_value2>` `<string_value3>` 为表数据源的名称。

### 查询数据源
dataSource.type=query时，参数：
```
{
    "type":"query",
    "query":{
		//Query
    }   
}
```
可以进行查询的嵌套。
