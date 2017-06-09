# Tindex-Query-Json `dataSource`属性详情如下

## `DataSource` 数据源

&#160; &#160; &#160; &#160;数据源相当于数据库中的表。     

`dataSource.type`可选项：`table`,`query`,`union`, 也可以是一个字符串()。

###  1. `Table DataSource`
&#160; &#160; &#160; &#160;`JSON`示例如下：    
```
{
    "type":"table",  
    "name":<string_value>
}
```
&#160; &#160; &#160; &#160;最常用的数据源，`<string_value>`为源数据源的名称，类似关系数据库中的表名。

### 2. `Union DataSource`
&#160; &#160; &#160; &#160;`JSON`示例如下：
```
{
    "type": "union",
    "dataSources": [<string_value1>,<string_value2>,... ]
}
```
&#160; &#160; &#160; &#160;该数据源连接两个或多个表数据，`<string_value1>` `<string_value2>` 为表数据源的名称。`Union DataSource`应该有相同的`schema`。`Union Queries`应该发送到代理/路由器节点，并不受历史节点直接支持。

### 3. `Query DataSource`
&#160; &#160; &#160; &#160;`JSON`示例如下：
```
{
    "type":"query",
    "query":{<query>}   
}
```
&#160; &#160; &#160; &#160;可以进行查询的嵌套。