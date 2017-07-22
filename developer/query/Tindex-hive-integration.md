### 1.单表查询原始数据，默认会加limit 1000  
```
select * from userinfo;  
```
userinfo是Tindex中的表，在hive中建立的外部表；

### 2.单表的groupby查询  
```
select userid from userinfo group by userid
```

userinfo是Tindex中的表，在hive中建立的外部表；  

### 3.对Tindex的表做groupby查询后，与hive表做join查询  
```
select u.province, count(*) 
    from (select userid from userinfo group by userid) a 
    join users u 
    on u.userid=a.userid 
    group by u.province;  
```
userinfo是Tindex中的表，在hive中建立的外部表；users是hive中的表  

### 4.对Tindex中的两张表分别做groupby查询后，再在hive中对结果做join操作  
```
select w.province, w.cnt, u.province, u.sumscore   
	from (select province, count(*) cnt 
	        from events 
	        where EventAction='唤醒' and `__time` > '2017-05-20' 
	        group by province) w   
	join   
	(select province, sum(score) sumscore 
	    from userinfo 
	    group by province) u     
	on w.province = u.province;   
```
events和userinfo都是Tindex中的表，在hive中建立对应的外部表  

### 5.对Tindex中的三张表分别做groupby查询后，再在hive中对结果做join操作
```
select w.province, w.cnt, u.province, u.sumscore,e.agesum   
	from (select province, count(*) cnt 
	        from sdk_reports 
	        where EventAction='唤醒' and `__time` > '2017-05-20' 
	        group by province) w 
	join (select province, sum(score) sumscore 
	        from userinfo 
	        group by province) u 
	on w.province = u.province 
	join (select province, sum(age) agesum 
	        from events 
	        group by province) e 
	on e.province = u.province ; 
```
sdk_reports、events和userinfo都是Tindex中的表，在hive中建立对应的外部表

### 6.多层嵌套查询
```
select u.num, u.userid, u.birthday, u.province, 
            u.score, w.city, w.province, w.sessionid 
	from (select userid, city, province, sessionid 
		from (select userid, city, province, sessionid,nation 
		        from sdk_reports 
		        where province='湖南省' 
		        limit 1000) wrt 
		where nation='中国' limit 1000) w 
	join 
	(select num, userid, birthday, province, score 
	    from userinfo 
	    where num < 1000 
	    limit 10000) u 
	on w.userid=u.userid 
	where u.num < 1000 
	limit 2000;
```


### Tindex支持的聚合函数：
count/count(distinct)  
sum(column):可以作用于int、long、float类型的列  
max(column)/min(column)：可以作用于int、long、float、date类型的列  

### 注意事项
建议不要在hive中执行需要查询Tindex表中的原始数据的操作，特别是在join操作中。

