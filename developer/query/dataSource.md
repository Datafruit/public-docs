# Tindex-Query-Json `dataSource`属性详情如下

## `DataSource` 数据源

数据源相当于数据库中的表。     

- `DataSource` 属性详情如下：
  - [`Table`](#Table)
  - [`Union`](#Union)
  - [`Query`](#Query)  

  也可以是一个字符串。

### <a id="Table" href="Table"></a>  1. `Table DataSource`
`JSON`示例如下：    
```
{
    "type":"table",  
    "name":<string_value>
}
```
最常用的数据源，`<string_value>`为源数据源的名称，类似关系数据库中的表名。

### <a id="Union" href="Union"></a> 2. `Union DataSource`
`JSON`示例如下：
```
{
    "type": "union",
    "dataSources": [<string_value1>,<string_value2>,... ]
}
```
该数据源连接两个或多个表数据，`<string_value1>` `<string_value2>` 为表数据源的名称。`Union DataSource`应该有相同的`schema`。`Union Queries`应该发送到代理/路由器节点，并不受历史节点直接支持。

### <a id="Query" href="Query"></a> 3. `Query DataSource`
`JSON`示例如下：
```
{
    "type":"query",
    "query":{<query>}   
}
```
可以进行查询的嵌套。