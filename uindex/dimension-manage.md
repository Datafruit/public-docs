# uindex维度动态管理接口
## 查看数据源spec信息
`curl http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/dimensions/{datasource_name}`

## 增加维度
`curl -X POST 'http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/dimensions/add/{datasource_name}'  -H 'Content-Type:application/json' -d '["i_age","d_score"]'`

## 删除维度
`curl -X POST 'http://{hmaster_ip}:8086/druid/hmaster/v1/datasources/dimensions/delete/{datasource_name}'  -H 'Content-Type:application/json' -d '["i_age","d_score"]'`

## 维度类型说明:  
新增维度命名规则：类型_维度名，例如年龄：i_age。  

| 前缀 |  类型   | 值 | 例子 |  
|----:| -----  | ----- | ---- |  
|  i  | int类型 | 单值 | i_age  |  
| mi  | int类型 | 多值 | mi_num  |  
| l   | long类型| 单值 | l_duration |  
| ml  | long类型| 多值 | ml_timestamp |  
|f|float类型|单值|f_avg  |  
|mf|float类型|多值|mf_score  |  
|d|double类型|单值|d_value  |  
|md|double类型|多值|md_value  |  
|s|string类型|单值|s_name  |  
|ms|string类型|多值|ms_name  |  

其他：string类型，多值，例如：name，没有指定类型，默认是string类型，并且是多值。  
