# Hive对Uindex多值列的支持
## 建立Hive外部表
建表语句:
```
CREATE EXTERNAL TABLE if not exists test_multi_col(
distinct_id string
)
row format delimited collection items terminated by '\u0001'  
STORED BY 'io.druid.hyper.hive.DruidStorageHandler'
TBLPROPERTIES ("druid.datasource" = "test_multi_col");  
```
通过`row format delimited collection items terminated by`指定数据列的分隔符, 避免插入多值列数据时出现format无法识别的错误
## 单条多值列数据插入示例
利用Hive的`array`函数实现数据的拼接,形成多值列数据
```
insert into schedule_test1(distinct_id,ms_testms) select  '00000000000000000189', array(t.page_name, t.pro_id) from tag_test t limit 1;
```
## 批量多值列数据插入示例
利用Hive的`collect_list/collect_set`函数实现groupBy查询后的数据收集,形成多值列数据
```
insert into test_multi_col(distinct_id,ms_test2) 
    select  t2.distinct_id, collect_set(t2.pro_type) from 
        (select a.distinct_id as distinct_id ,a.pro_type as pro_type from 
            (select distinct_id,pro_type, row_number() over(partition by distinct_id order by count(*) desc) rank from tag_test
                where event_type = '浏览' and pro_type <> 'null'  group by distinct_id,pro_type) a 
        where a.rank < 3) t2 
    group by t2.distinct_id;
```