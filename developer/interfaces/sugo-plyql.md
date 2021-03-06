
# Sugo PlyQL

 - [简介](#intro)
 - [示例](#example)
 - [操作符](#operators)
 - [函数](#functions)
 - [其他函数](#otherFunc)
 - [聚合](#aggregations)
 - [JDBC 驱动文档](/developer/interfaces/jdbc.md)
 - [下载部署sugo-plyql](/developer/interfaces/download-plyql.md)

---

<a id="intro" href="intro"></a>
plyql是类似SQL的语言，可以解析成sugo-plywood的表达并执行。

plyql目前支持`SELECT`，`DESCRIBE`，和`SHOW TABLES`查询。

---

### 启用服务方式

  - [终端命令模式](#dos)
  - [HTTP REST API模式](#rest)
    - [`/plyql`SQL查询接口](#rest-plyql)
    - [`/tindex`Tindex原始QueryJSON查询接口](#rest-tindex)
    - [`/get-query`SQL转Tindex-QueryJSON接口](#rest-get-query)
  - [JDBC驱动模式](/developer/interfaces/jdbc.md)

---

### <a id="example" href="#example"></a> 示例
---

  > 以下使用实例以`终端命令模式`为例子，其他模式的SQL调用一样

  > 对于这些示例，我们查询一个Druid代理节点IP为`192.168.60.100`，端口为`8082`

  > 我们将地址（192.168.60.100:8082）传递给`--host`（`-h`）选项。

  > 将所有的SQL比如`SHOW TABLES`，`DESCRIBE TABLE_NAME`，`SELECT COUNT(*) FROM TABLE_NAME`传递给`--query`（`-q`）选项。
  
  > `注：暂不支持多表JOIN查询，只支持单表查询，具体可参考下面示例`

  - [查询数据源](#show-tables)
    - [查询数据源列定义结构](#desc)
  - [SQL查询调用](#query)
    - [普通查询](#query-normal)
    - [条件过滤查询](#query-where)
    - [分组查询](#query-group-by)
    - [分组GROUPING SETS查询](#query-group-by-grouping-sets)
    - [聚合查询](#query-aggregation)
    - [聚合查询带where](#agg-where)
    - [函数查询](#query-function)
    - [CASE-WHEN查询](#case-when)
    - [having查询](#query-having)
    - [LOOKUP_IN过滤查询](#query-lookup)
    - [高级查询](#adv-query)
    - [所有查询示例](#query-all)

### <a id="dos" href="#dos"></a> 终端命令模式

#### <a id="show-tables" href="show-tables"></a> 查询所有数据源列表

> 查询当前节点所有数据源列表的例子：


```sql
plyql -h 192.168.60.100:8082 -q 'SHOW TABLES'
```

返回

```
┌────────────────────────────────────┐
│ Tables_in_database                 │
├────────────────────────────────────┤
│ COLUMNS                            │
│ SCHEMATA                           │
│ TABLES                             │
│ sugo_test1                         │
│ sugo_test2                         │
│ sugo                               │
└────────────────────────────────────┘
```

#### <a id="desc" href="desc"></a> 查看数据源列定义结构

```sql
plyql -h 192.168.60.100:8082 -q 'DESCRIBE sugo'
```

返回数据源的列定义：

```
┌──────────────┬────────┬──────┬─────┬─────────┬───────┐
│ Field        │ Type   │ Null │ Key │ Default │ Extra │
├──────────────┼────────┼──────┼─────┼─────────┼───────┤
│ HEAD_ARGS    │ STRING │ YES  │     │ NULL    │       │
│ HEAD_IP      │ STRING │ YES  │     │ NULL    │       │
│ __time       │ TIME   │ YES  │     │ NULL    │       │
│ app_id       │ STRING │ YES  │     │ NULL    │       │
│ duration     │ NUMBER │ YES  │     │ NULL    │       │
│ is_active    │ STRING │ YES  │     │ NULL    │       │
│ is_installed │ STRING │ YES  │     │ NULL    │       │
│ client_time  │ TIME   │ YES  │     │ NULL    │       │
└──────────────┴────────┴──────┴─────┴─────────┴───────┘
```

<a id="query" href="query"></a>

#### <a id="query-normal" href="query-normal"></a> 普通查询

这里是一个简单的查询，获取所以的原始明细数据限定2条。

```sql
plyql -h 192.168.60.100:8082 -q 'SELECT * FROM sugo limit 2'
```

返回:

```
┌────────────┬───────────────┬──────────────────────┬─────────────────────┬──────────┬───────────┬──────────────┬──────────────────────┐
│ HEAD_ARGS  │ HEAD_IP       │ __time               │ app_id              │ duration │ is_active │ is_installed │ client_time          │
├────────────┼───────────────┼──────────────────────┼─────────────────────┼──────────┼───────────┼──────────────┼──────────────────────┤
│ topic=sugo │ 192.168.0.210 │ 2016-09-19T06:36:43Z │ 6284164581582112235 │ 7682     │ 1         │ 1            │ 2016-12-24T16:00:00Z │
│ topic=sugo │ 192.168.0.210 │ 2016-09-19T06:36:43Z │ 6284164581582112235 │ 7682     │ 1         │ 1            │ 2016-12-24T16:00:00Z │
└────────────┴───────────────┴──────────────────────┴─────────────────────┴──────────┴───────────┴──────────────┴──────────────────────┘
```

#### <a id="query-where" href="query-where"></a> 条件过滤查询

查询某个时间段范围的数据

> 注意这里的时间ISO格式

```sql
plyql -h 192.168.60.100:8082 -q '
SELECT page as pg, 
is_active as flag 
FROM sugo
WHERE is_active='1' AND __time > "2016-01-24T16:00:00Z" AND __time < "2016-09-24T16:00:00Z"
LIMIT 4
'
```

结果集:
  
```
┌─────────────────────┬─────┐
│ pg                  │flag │
├─────────────────────┼─────┤
│ Jeremy Corbyn       │ 1   │
│ Jeremy 111111       │ 1   │
│ Jeremy 222222       │ 1   │
│ Jeremy 333333       │ 1   │
└─────────────────────┴─────┘
```

#### <a id="query-lookup" href="query-lookup"></a> LOOKUP_IN条件过滤查询

将LOOKUP作为过滤条件查询

```sql
plyql -h 192.168.60.100:8082 -q '
SELECT page as pg, 
is_active as flag
FROM sugo
WHERE UserID LOOKUP_IN("lookup_id") AND SID LOOKUP_IN "looup_id2" -- 或者可以去掉括号 UserID LOOKUP_IN "lookup_id"
LIMIT 4
'
```

结果集:
  
```
┌─────────────────────┬─────┐
│ pg                  │flag │
├─────────────────────┼─────┤
│ Jeremy Corbyn       │ 1   │
│ Jeremy 111111       │ 1   │
│ Jeremy 222222       │ 1   │
│ Jeremy 333333       │ 1   │
└─────────────────────┴─────┘
```

#### <a id="query-group-by" href="query-group-by"></a> 分组查询

```sql
plyql -h 192.168.60.100:8082 -q '
SELECT page as pg, 
COUNT() as cnt 
FROM sugo_test 
WHERE "2015-09-12T00:00:00" <= __time AND __time < "2015-09-13T00:00:00"
GROUP BY page
ORDER BY cnt DESC 
LIMIT 5;
'
```

结果集:
  
```
┌─────────────────────┬─────┐
│ pg                  │ cnt │
├─────────────────────┼─────┤
│ Jeremy Corbyn       │ 2   │
│ Jeremy 111111       │ 2   │
│ Jeremy 222222       │ 2   │
│ Jeremy 333333       │ 2   │
└─────────────────────┴─────┘
```

#### <a id="query-group-by-grouping-sets" href="query-group-by-grouping-sets"></a> 分组查询

```sql
plyql -h 192.168.60.100:8082 -q '
SELECT province as pg,
age,
date_format(__time,"%Y-%m-%d") time
COUNT() as cnt
FROM sugo_test
WHERE "2015-09-12T00:00:00" <= __time AND __time < "2015-09-13T00:00:00"
GROUP BY GROUPING SETS (1, (2, 3))
ORDER BY cnt DESC
LIMIT 5;
'
```

结果集:
  
```
┌─────┬──────────┬──────┬────────────┬───────┐
│ GID │ province │ age  │ time       │ total │
├─────┼──────────┼──────┼────────────┼───────┤
│ 0   │ 上海市    │ NULL │ 2017-05-13 │ 25768 │
│ 0   │ 云南省    │ NULL │ 2017-05-13 │ 25595 │
│ 0   │ 内蒙古    │ NULL │ 2017-05-13 │ 25756 │
│ 0   │ 北京市    │ NULL │ 2017-05-13 │ 25490 │
│ 1   │ NULL     │ 11   │ 2017-05-13 │ 43524 │
│ 1   │ NULL     │ 12   │ 2017-05-13 │ 43428 │
│ 1   │ NULL     │ 13   │ 2017-05-13 │ 43726 │
│ 1   │ NULL     │ 14   │ 2017-05-13 │ 43606 │
│ 1   │ NULL     │ 15   │ 2017-05-13 │ 43376 │
│ 1   │ NULL     │ 16   │ 2017-05-13 │ 43599 │
│ 1   │ NULL     │ 29   │ 2017-05-19 │ 49927 │
└─────┴──────────┴──────┴────────────┴───────┘
```

##### 时间分组

通过`TIME_BUCKET`函数可以对时间分解

```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT SUM(added) as total 
FROM sugo_test 
GROUP BY TIME_BUCKET(__time, PT6H, "Asia/Shanghai");
'
```

返回:

```
┌─────────────────────────────────────────────┬───────┐
│ split0                                      │ total │
├─────────────────────────────────────────────┼───────┤
│ [2017-02-06T22:00:00Z,2017-02-07T04:00:00Z] │ 8337  │
│ [2017-02-07T04:00:00Z,2017-02-07T10:00:00Z] │ 21600 │
│ [2017-02-07T10:00:00Z,2017-02-07T16:00:00Z] │ 21600 │
│ [2017-02-07T16:00:00Z,2017-02-07T22:00:00Z] │ 21600 │
│ [2017-02-07T22:00:00Z,2017-02-08T04:00:00Z] │ 21600 │
│ [2017-02-08T04:00:00Z,2017-02-08T10:00:00Z] │ 21600 │
│ [2017-02-08T10:00:00Z,2017-02-08T16:00:00Z] │ 21600 │
│ [2017-02-08T16:00:00Z,2017-02-08T22:00:00Z] │ 21600 │
│ [2017-02-08T22:00:00Z,2017-02-09T04:00:00Z] │ 21600 │
└─────────────────────────────────────────────┴───────┘
```

注意分组列如果没有选择但仍有返回, 列如`TIME_BUCKET(__time, PT1H, 'Asia/Shanghai') as 'split'`

是查询列中的一个。

时间分割也支持，这里是一个例子：
```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT TIME_PART(__time, HOUR_OF_DAY, "Asia/Shanghai") as HourOfDay, 
SUM(added) as TotalAdded 
FROM wikipedia 
GROUP BY 1 
ORDER BY TotalAdded DESC LIMIT 3;
'
```

注意，这个 `GROUP BY` 指的是选择中的第一列。

这将返回：

```json
┌────────────┬───────────┐
│ TotalAdded │ HourOfDay │
├────────────┼───────────┤
│ 8077302    │ 10        │
│ 5998730    │ 17        │
│ 5210222    │ 18        │
└────────────┴───────────┘
```

它也是可能做到多维度分组查询的

```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT TIME_BUCKET(__time, PT1H, "Asia/Shanghai") as Hour, 
page as PageName, 
SUM(added) as TotalAdded 
FROM wikipedia 
GROUP BY 1, 2 
ORDER BY TotalAdded DESC 
LIMIT 3;
'
```

返回:

```json
[
  {
    "TotalAdded": 242211,
    "PageName": "Wikipedia‐ノート:即時削除の方針/過去ログ16",
    "Hour": {
      "start": "2015-09-12T15:00:00.000Z",
      "end": "2015-09-12T16:00:00.000Z",
      "type": "TIME_RANGE"
    }
  },
  {
    "TotalAdded": 232941,
    "PageName": "Користувач:SuomynonA666/Заготовка",
    "Hour": {
      "start": "2015-09-12T14:00:00.000Z",
      "end": "2015-09-12T15:00:00.000Z",
      "type": "TIME_RANGE"
    }
  },
  {
    "TotalAdded": 214017,
    "PageName": "User talk:Estela.rs",
    "Hour": {
      "start": "2015-09-12T12:00:00.000Z",
      "end": "2015-09-12T13:00:00.000Z",
      "type": "TIME_RANGE"
    }
  }
]
```

#### <a id="query-aggregation" href="query-aggregation"></a> 聚合查询

```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT province as pro,
SUM(added) as sumAdded,
MAX(__time) as maxTime,
AVG(added) as avgAdded
FROM sugo_test
LIMIT 5;
'
```
返回：

```
┌──────────┬───────────────┬──────────┐
│ sumAdded │ maxTime       │ avgAdded │
├──────────┼───────────────┼──────────┤
│ 2160     │ 1487812861000 │ 1920     │
└──────────┴───────────────┴──────────┘
```

#### <a id="agg-where" href="agg-where"></a> 聚合函数带where查询

```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
  SELECT COUNT(*) total ,
  COUNT(* where event_action in ("启动" , "唤醒")) filterTotal
  FROM sugo_test
'
```
返回：

```
┌──────────┬───────────────┐
│ total    │ filterTotal   │
├──────────┼───────────────┤
│ 21605    │ 3223          │
└──────────┴───────────────┘
```

```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
  SELECT TIME_BUCKET(__time, P1D, "Asia/Shanghai") time,
  COUNT(*) total ,
  COUNT(* where event_action in ("启动" , "唤醒")) filterTotal
  FROM sugo_test
  GROUP BY 1
'
```
返回：

```
┌────────────────────┬────────┬─────────────┐
│ time               │ total  │ filterTotal │
├────────────────────┼────────┼─────────────┤
│ 2016-07-24 16:00:00│ 234334 │ 1920        │
└────────────────────┴────────┴─────────────┘
```



#### <a id="query-function" href="query-function"></a> 函数查询

```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT
SUBSTR(Province, 1, 1) subPro, 
CONCAT(Province, "_SUFF")
FROM sugo_test
LIMIT 5;
'
```
返回：

```
┌────────┬─────────────┐
│ subPro │ pro         │
├────────┼─────────────┤
│ 宁     │ 辽宁省_SUFF │
│ 西     │ 广西省_SUFF │
│ 西     │ 陕西省_SUFF │
│ 建     │ 福建省_SUFF │
│ 苏     │ 江苏省_SUFF │
└────────┴─────────────┘
```

##### QUANTILE函数

支持对直方图的分位数。

假设您想要使用直方图计算 0.95 分位数的三角洲筛选的城市是旧金山。

```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT 
QUANTILE(delta_hist WHERE cityName = "San Francisco", 0.95) as P95 
FROM wikipedia;
'
```

#### <a id="case-when" href="case-when"></a> CASE WHEN 查询

```shell
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT
CASE Province WHEN "广东省" THEN "广东省" ELSE "其他省份" END AS caseProvince,
COUNT(*) AS "Count"
FROM sugo_test
WHERE Nation = "中国"
GROUP BY 1
ORDER BY Count DESC 
LIMIT 3;
'
```

或者

```shell
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT
CASE WHEN Province="广东省" THEN "广东省" ELSE "其他省份" END AS caseProvince, 
COUNT(*) AS "Count"
FROM sugo_test
WHERE Nation = "中国" 
GROUP BY 1 
ORDER BY Count DESC 
LIMIT 3;
'
```

返回:

```
┌──────────────┬───────────┐
│ caseProvince │ Count     │
├──────────────┼───────────┤
│ 其他省份      │ 216958713 │
│ 广东省        │ 6781726   │
└──────────────┴───────────┘
```


#### <a id="query-having" href="query-having"></a> having查询

这里的HAVING条件跟传统的SQL有点区别，就是只能指定SELET里聚合过的或者数值列类型的

```shell
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT province as pro, 
COUNT() as cnt 
FROM sugo_test
GROUP BY province 
HAVING cnt > 1
ORDER BY cnt DESC 
LIMIT 5;
'
```

返回:

```
┌────────┬───────┐
│ pro    │ cnt   │
├────────┼───────┤
│ 局域网  │ 49311 │
│ 广东   │ 30407 │
│ 广西   │ 9213  │
│ 福建   │ 3134  │
│ 香港   │ 956   │
└────────┴───────┘
```


#### <a id="adv-query" href="adv-query"></a> 高级查询

这里是一个高级的示例，获取前 5 页编辑时间。

PlyQL 的一个显著特征是它对待 （aka 表） 的数据集作为可以嵌套在另一个表的只是另一种数据类型。

这使我们能够嵌套查询数据像这样︰
```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT page as Page, 
COUNT() as cnt, 
(
  SELECT 
  SUM(added) as TotalAdded 
  GROUP BY TIME_BUCKET(__time, PT1H, "Asia/Shanghai") 
  LIMIT 3 -- only get the first 3 hours to keep this example output small
) as "ByTime" 
FROM wikipedia 
GROUP BY page 
ORDER BY cnt DESC 
LIMIT 5;
'
```

返回:

```json
[
  {
    "cnt": 314,
    "Page": "Jeremy Corbyn",
    "ByTime": [
      {
        "TotalAdded": 1075,
        "split0": {
          "start": "2015-09-12T01:00:00.000Z",
          "end": "2015-09-12T02:00:00.000Z",
          "type": "TIME_RANGE"
        }
      },
      {
        "TotalAdded": 0,
        "split0": {
          "start": "2015-09-12T07:00:00.000Z",
          "end": "2015-09-12T08:00:00.000Z",
          "type": "TIME_RANGE"
        }
      },
      {
        "TotalAdded": 10553,
        "split0": {
          "start": "2015-09-12T08:00:00.000Z",
          "end": "2015-09-12T09:00:00.000Z",
          "type": "TIME_RANGE"
        }
      }
    ]
  },
  {
    "cnt": 255,
    "Page": "User:Cyde/List of candidates for speedy deletion/Subpage",
    "ByTime": [
      {
        "TotalAdded": 73,
        "split0": {
          "start": "2015-09-12T00:00:00.000Z",
          "end": "2015-09-12T01:00:00.000Z",
          "type": "TIME_RANGE"
        }
      },
      {
        "TotalAdded": 3363,
        "split0": {
          "start": "2015-09-12T01:00:00.000Z",
          "end": "2015-09-12T02:00:00.000Z",
          "type": "TIME_RANGE"
        }
      },
      {
        "TotalAdded": 336,
        "split0": {
          "start": "2015-09-12T02:00:00.000Z",
          "end": "2015-09-12T03:00:00.000Z",
          "type": "TIME_RANGE"
        }
      }
    ]
  },
  "... results omitted ..."
]
```

#### <a id="query-all" href="query-all"></a> 所有查询示例

```sql
   SELECT
    a = b AS aISb1,
    a IS b AS aISb2,
    a <=> b AS aISb3,
    COUNT() Count1,
    COUNT(*) AS Count2,
    COUNT(1) AS Count3,
    COUNT(\`visitor\`) AS Count4,
    MATCH(\`visitor\`, "[0-9A-F]") AS 'Match',
    CUSTOM_TRANSFORM(\`visitor\`, "visitor_custom") AS 'CustomTransform',
    SUM(added) AS 'TotalAdded',
    DATE '2014-01-02' AS 'Date',
    SUM(\`wiki\`.\`added\`) / 4 AS TotalAddedOver4,
    NOT(true) AS 'False',
    -SUM(added) AS MinusAdded,
    ABS(MinusAdded) AS AbsAdded,
    ABSOLUTE(MinusAdded) AS AbsoluteAdded,
    POWER(MinusAdded, 0.5) AS SqRtAdded,
    POW(MinusAdded, 0.5) AS SqRtAdded2,
    SQRT(TotalAddedOver4) AS SquareRoot,
    EXP(0) AS One,
    +SUM(added) AS SimplyAdded,
    QUANTILE(added, 0.5) AS Median,
    QUANTILE(added, 0.5, "resolution=400") AS MedianTuned,
    COUNT_DISTINCT(visitor) AS 'Unique1',
    COUNT( DISTINCT visitor) AS 'Unique2',
    COUNT(DISTINCT(visitor)) AS 'Unique3',
    TIME_BUCKET(time, PT1H) AS 'TimeBucket',
    TIME_FLOOR(time, PT1H) AS 'TimeFloor',
    TIME_SHIFT(time, PT1H) AS 'TimeShift1',
    TIME_SHIFT(time, PT1H, 3) AS 'TimeShift3',
    TIME_RANGE(time, PT1H) AS 'TimeRange1',
    TIME_RANGE(time, PT1H, 3) AS 'TimeRange3',
    DATE_FORMAT(time, '%Y-%m-%d', 'Asia/Shanghai'),
    OVERLAP(x, y) AS 'Overlap',
    IF(x, "hello", \`world\`) AS 'If1',
    CASE moon WHEN 1 THEN 'one' WHEN 2 THEN 'two' ELSE 'more' END AS 'Case1',
    CASE WHEN moon > 13 THEN 'T' ELSE 'F' END AS 'Case2',
    CASE WHEN moon = 13 THEN 'TheOne' END AS 'Case3',
    CUSTOM('blah') AS 'Custom1',
    CUSTOM_AGGREGATE('blah') AS 'Custom2'
    FROM \`wiki\`
    WHERE \`language\`="en"  AND UserID LOOKUP_IN('lookup_id');  -- This is just some comment
```


##### --interval 参数

Plyql有一个选项 `--interval` (`-i`) 自动过滤器加上`interval`间隔时间。

如果您不想键入时间筛选器，则这样设置非常有用。

```sql
plyql -h 192.168.60.100:8082 -i P1Y -q '
SELECT page as pg, 
COUNT() as cnt 
FROM wikipedia 
GROUP BY page 
ORDER BY cnt DESC 
LIMIT 5;
'
```

## <a id="operators" href=“operators”></a> 运算符

名称                    | 描述
------------------------|-------------------------------------
+                       | 加法运算符
-                       | 减号运算符 / 一元求反
*                       | 乘法运算符
/                       | 除法运算符
=, IS                   | 等于运算符
!=, <>, IS NOT          | 不等于运算符
>=                      | 大于或等于运算符
>                       | 大于运算符
<=                      | 小于或等于运算符
<                       | 小于运算符
BETWEEN ... AND ...     | 检查一个值是否在值范围内
NOT BETWEEN ... AND ... | 检查一个值是否不在值范围内
LIKE, CONTAINS          | 简单的模式(模糊)匹配
NOT LIKE, CONTAINS      | 否定的简单模式(模糊)匹配
REGEXP                  | 模式匹配使用正则表达式
NOT REGEXP              | 正则表达式的否定
NOT, !                  | 取否
AND                     | 逻辑并
OR                      | 逻辑或

## <a id="functions" href="functions"></a> 函数

<a id="TIME_BUCKET" href="#TIME_BUCKET">#</a>
**TIME_BUCKET**(operand, duration, timezone)

将时间在给定的`timezone(时区)`中按给定的`duration(粒度)`查询

示例: `TIME_BUCKET(time, 'P1D', 'Asia/Shanghai')`

这将把`time`变量装入日期块，其中按`Asia/Shanghai`时区的天粒度定义。

<a id="TIME_PART" href="#TIME_PART">#</a>
**TIME_PART**(operand, part, timezone)

按指定的时区获取对应参数的时间参数值

例如: `TIME_PART(time, 'DAY_OF_YEAR', 'Asia/Shanghai')`
这将把'time`变量分成（整数）数字，表示一年中的哪一天。
可能的部分值是:

* `SECOND_OF_MINUTE`, `SECOND_OF_HOUR`, `SECOND_OF_DAY`, `SECOND_OF_WEEK`, `SECOND_OF_MONTH`, `SECOND_OF_YEAR`
* `MINUTE_OF_HOUR`, `MINUTE_OF_DAY`, `MINUTE_OF_WEEK`, `MINUTE_OF_MONTH`, `MINUTE_OF_YEAR`
* `HOUR_OF_DAY`, `HOUR_OF_WEEK`, `HOUR_OF_MONTH`, `HOUR_OF_YEAR`
* `DAY_OF_WEEK`, `DAY_OF_MONTH`, `DAY_OF_YEAR`
* `WEEK_OF_MONTH`, `WEEK_OF_YEAR`
* `MONTH_OF_YEAR`
* `YEAR`


<a id="TIME_FLOOR" href="#TIME_FLOOR">#</a>
**TIME_FLOOR**(operand, duration, timezone)

在给定的`timezone(时区)`中将时间置于最近的`duration(粒度)`。
示例: `TIME_FLOOR(time, 'P1D', 'Asia/Shanghai')`

这将把`time`变量置于一天的开始，其中天在`Asia/Shanghai`时区中定义。

<a id="TIME_SHIFT" href="#TIME_SHIFT">#</a>
**TIME_SHIFT**(operand, duration, step, timezone)

在给定的`timezone(时区)`中，通过`duration(粒度)` * `step`向前移动时间。
`step`可能是负数。

示例: `TIME_SHIFT(time, 'P1D', -2, 'Asia/Shanghai')`

这将在两天后移动`time`变量，其中天在`Asia/Shanghai`时区中定义。

<a id="TIME_RANGE" href="#TIME_RANGE">#</a>
**TIME_RANGE**(operand, duration, step, timezone)

在给定的`timezone(时区)`中创建一个范围形式`time`和一个`duration` *`step`远离`time`的点。
`step`可能是负数。

示例: `TIME_RANGE(time, 'P1D', -2, 'Asia/Shanghai')`

这将在两天后移动`time`变量，其中天在`Asia/Shanghai`时区中定义，并创建一个时间-2 * P1D - >时间范围。

<a id="SUBSTR" href="#SUBSTR">#</a>
**SUBSTR**(*str*, *pos*, *len*)

从字符串*str*返回一个子串*len*个字符，从位置*pos*开始

<a id="CONCAT" href="#CONCAT">#</a>
**CONCAT**(*str1*, *str2*, ...)

返回由连接参数产生的字符串。 可以有一个或多个参数

<a id="EXTRACT" href="#EXTRACT">#</a>
**EXTRACT**(*str*, *regexp*)

返回第一个匹配的组，结果窗体匹配 *regexp* 到 *str*

<a id="LOOKUP" href="#LOOKUP">#</a>
**LOOKUP**(*str*, *lookup-namespace*)

返回键的值 *str* 内 *查找-命名空间*

<a id="IFNULL" href="#IFNULL">#</a>
**IFNULL**(*expr1*, *expr2*)

返回 *expr* 如果不为空，否则返回 *expr2*

<a id="FALLBACK" href="#FALLBACK">#</a>
**FALLBACK**(*expr1*, *expr2*)

这个等同于 **IFNULL**(*expr1*, *expr2*)

<a id="OVERLAP" href="#OVERLAP">#</a>
**OVERLAP**(*expr1*, *expr2*)

检查 *expr1* 和 *expr2* 是否重叠

<a id="CUSTOM_TRANSFORM" href="#CUSTOM_TRANSFORM">#</a>
**CUSTOM_TRANSFORM(*expr1*, *custom_name*)**

调用自定义转换函数处理数据

<a href="cast" id="cast">#</a>
**CAST** (*expr1*, *op*)
将expr1转换为其他类型, `op包含：'CHAR'(string), 'SIGNED'(number)`; 例如将字符串转换为number类型： CAST(expr1, 'SIGNED')
`SELECT CAST(\`commentLength\` AS CHAR) as castedString, CAST(\`commentLengthStr\` AS SIGNED) as castedNumber FROM \`wiki\``

<a id="DATE_FORMAT" href="#DATE_FORMAT">#</a> DATE_FORMAT(expr, format, timezone)

 时间格式化函数处理, 例如：`DATE_FORMAT(time, '%Y-%m-%d', 'Asia/Shanghai')` 目前支持格式：

- format：  
  > `%Y-%m-%d %H:%i:%s`  
  > `%Y-%m-%d %H:%i`  
  > `%Y-%m-%d %H`  
  > `%Y-%m-%d`  
  > `%Y-%m`  
  > `%Y`  

### <a id="otherFunc" href="#otherFunc">#</a> 其他函数
`YEAR`, `MONTH`, `WEEK_OF_YEAR` `DAY_OF_YEAR` `DAY_OF_MONTH` `DAY_OF_WEEK` `HOUR` `MINUTE` `SECOND` `DATE` `TIMESTAMP` `DATE_ADD` `DATE_SUB` `FROM_UNIXTIME` `UNIX_TIMESTAMP`

### 数学函数

<a id="ABS" href="#ABS">#</a>
**ABS**(*expr*)

返回*expr*的绝对值。

<a id="POW" href="#POW">#</a>
**POW**(*expr1*, *expr2*)

返回 *expr1* 的幂 *expr2*。

<a id="POWER" href="#POWER">#</a>
**POWER**(*expr1*, *expr2*)

这个等同于 **POW**(*expr1*, *expr2*)

<a id="SQRT" href="#SQRT">#</a>
**SQRT**(*expr*)

返回*expr*的平方根

<a id="EXP" href="#EXP">#</a>
**EXP**(*expr*)

返回 e（自然对数的基数） 的值*expr*的幂。


## <a id="aggregations" href="aggregations"></a> 聚合函数

<a id="COUNT" href="#COUNT">#</a>
**COUNT**(*expr?*)

如果在没有表达式的情况下使用（或者作为`COUNT(*)`）返回行数的计数。
当提供表达式时，返回*expr*不为null的行的计数。

<a id="COUNT" href="#COUNT">#</a>
**COUNT**(**DISTINCT** *expr*), **COUNT_DISTINCT**(*expr*) 

返回具有不同*expr*值的行数的计数。

<a id="SUM" href="#SUM">#</a>
**SUM**(*expr*) 

返回所有*expr*值的总和。

<a id="MIN" href="#MIN">#</a>
**MIN**(*expr*) 

返回所有*expr*值的最小值。

<a id="MAX" href="#MAX">#</a>
**MAX**(*expr*) 

返回所有*expr*值的最大值。

<a id="AVG" href="#AVG">#</a>
**AVG**(*expr*)

返回所有*expr*值的平均值。

<a id="QUANTILE" href="#QUANTILE">#</a>
**QUANTILE**(*expr*, *quantile*)

返回所有*expr*值的上层*分位数*。

<a id="CUSTOM" href="#CUSTOM">#</a>
**CUSTOM**(*custom_name*)

返回名为*custom_name*的用户定义聚合。

<a id="CUSTOM_AGGREGATE" href="#CUSTOM_AGGREGATE">#</a>
**CUSTOM_AGGREGATE(*custom_name*)**

调用自定义的聚合函数

## <a id="rest" href="#rest"></a> HTTP REST API模式

#### 启动HTTP REST APi

```
plyql -h 192.168.60.100  --json-server  8001
```

##### 1. <a id="rest-plyql" href-"rest-plyql"></a> HTTP SQL接口请求

发送post请求到 `http://192.168.60.100:8001/plyql`
```json
// post请求: application/json 参数
{
  "sql": "SHOW TABLES"
}
```

##### <a href="rest-tindex" href-"rest-tindex"></a> 2. HTTP 原生Tindex-JSON接口请求

发送post请求到 `http://192.168.60.100:8001/tindex`
```json
// post请求: application/json 参数
{
    "queryType": "lucene_timeseries",
    "dataSource": "events",
    "intervals": "1000/3000",
    "granularity": "all",
    "context": {
        "timeout": 60000,
        "groupByStrategy": "v2"
    },
    "aggregations": [
        {
            "name": "__VALUE__",
            "type": "lucene_count"
        }
    ]
}
```

##### 3. <a id="rest-get-query" href-"rest-get-query"></a> HTTP 将SQL转换为Tindex原生queryJSON接口

发送post请求到 `http://192.168.60.100:8001/get-query`

```json
  {
    sql: 'select * from events',
    // 可选参数
    scanQuery: false, // 是否scanQuery
    hasLimit: true,   // 是否包含limit
    timeout: 180000   // 设置timeout
  }
```

#### 更多Tindex查询类型请参考 [查询 Tindex-Query-Json](/developer/query/query.md)