# 摄入数据
目前提供三种类型的操作,分别是新增数据,修改数据,删除数据.类型由数据中的`action`指定.  
接口如下:  
`curl -X POST 'http://{hregionServer_ip}:8087/druid/regionServer/v1/push'  -H 'Content-Type:application/json' -d '{data}'`

在摄入数据时,客户端需要按主键(比如用户id)分片,将数据发送到相应的`regionServer`服务器.   
## 新增记录
新增记录操作的`action`值需要指定为`A`:  
```
{
    "action":"A",
    "dataSource":"test",
    "partitionNum":1,
    "values":["col1Value1\001col2Value1\001col3Value1","col1Value2\001col2Value2\001col3Value2"]
}
```
`dataSource`:数据源  
`partitionNum`:由客户端计算得出  
`values`:值之间的分隔符在创建`datasource`时指定的,比如分隔符为"\001"  
## 修改记录
修改记录操作的action值需要指定为"U":   
``` 
{
    "action":"U",
    "dataSource":"test",
    "partitionNum":1,
    "columns":["col2", "col3"],
    "values":["col2Value1\001col3Value1","col2Value2\001col3Value2"]
    "appendFlags":[[true,true],[true,false]]
}
```
`columns`:本次修改涉及的列  
`values`:被修改列对应的值  
`appendFlags`:修改的列如果是多值列,允许使用追加的方式更新.    
```
{
    "action":"D",
    "dataSource":"test",
    "partitionNum":1,
    "primaryValues":["id1", "id2", "id3"]
}
```
`primaryValues`:待删除的主键`id`,目前只能根据主键`id`删除.    
