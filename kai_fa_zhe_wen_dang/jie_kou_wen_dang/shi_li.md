# 示例

这些例子我们查询一个Druid的broker节点：192.168.60.100 端口： 8082.

首先，我们可以为数据源列表发出一个SHOW TABLES查询，我们将其传递到--query 或者 -q

MYSQL 查询Druid数据源列表

plyql -h 192.168.60.100:8082 -q '**SHOW** **TABLES**'
```javascript

HTTP接口请求

[http://192.168.60.100:8082/api/plyql/sql](http://192.168.60.100:8082/api/plyql/sql)

post请求: application/json 参数

{

"query":

"SHOW TABLES"

}

返回JSON:

{

"result": \[

{

"Tables\_in\_database": "wikipedia"

},

....

\]

}

查询Druid数据源表数据结构

plyql -h 192.168.60.100:8082 -q '**DESCRIBE** wikipedia'

返回数据源的列定义：

\[

{

"name": "\_\_time",

"type": "TIME"

},

{

"name": "added",

"type": "NUMBER",

"unsplitable": true

},

{

"name": "channel",

"type": "STRING"

},

{

"name": "cityName",

"type": "STRING"

},

{

"name": "comment",

"type": "STRING"

},

{

"name": "commentLength",

"type": "STRING"

},

{

"name": "count",

"type": "NUMBER",

"unsplitable": true

},

{

"name": "countryIsoCode",

"type": "STRING"

},

{

"name": "countryName",

"type": "STRING"

},

{

"name": "deleted",

"type": "NUMBER",

"unsplitable": true

},

{

"name": "delta",

"type": "NUMBER",

"unsplitable": true

},

{

"name": "deltaByTen",

"type": "NUMBER",

"unsplitable": true

},

{

"name": "delta\_hist",

"type": "NUMBER",

"special": "histogram"

},

"... results omitted ..."

\]

这里是一个简单的查询，获取最大的\_\_time信息。此查询显示数据库中最新事件的时间。

plyql -h 192.168.60.100:8082 -q '**SELECT** **MAX**\(\_\_time\) **AS** maxTime **FROM** wikipedia'

返回:

\[

{

"maxTime": {

"type": "TIME",

"value": "2015-09-12T23:59:00.000Z"

}

}

\]

现在你可能要检查，不同的标签趋势。

您可能会在这样的页面列上做分组：

plyql -h 192.168.60.100:8082 -q '

**SELECT** page **as** pg,

**COUNT**\(\) **as** cnt

**FROM** wikipedia

**GROUP** **BY** page

**ORDER** **BY** cnt **DESC**

**LIMIT** 5;

'

这将抛出一个错误，因为没有时间过滤指定plyql查询的范围。

这种行为可以禁用使用--allow eternity，但不推荐这样做，这样做时，当数据量过大时，它可以发出计算禁止查询。

再试一次，用一个时间过滤器：

plyql -h 192.168.60.100:8082 -q '

**SELECT** page **as** pg,

**COUNT**\(\) **as** cnt

**FROM** wikipedia

**WHERE** "2015-09-12T00:00:00" &lt;= **time AND **time &lt; "2015-09-13T00:00:00"

**GROUP** **BY** page

**ORDER** **BY** cnt **DESC**

**LIMIT** 5;

'

结果集:

\[

{

"cnt": 314,

"pg": "Jeremy Corbyn"

},

{

"cnt": 255,

"pg": "User:Cyde/List of candidates for speedy deletion/Subpage"

},

{

"cnt": 228,

"pg": "Wikipedia:Administrators' noticeboard/Incidents"

},

{

"cnt": 186,

"pg": "Wikipedia:Vandalismusmeldung"

},

{

"cnt": 160,

"pg": "Total Drama Presents: The Ridonculous Race"

}

\]

Plyql有一个选项 --interval \(-i\) 自动过滤器加上interval间隔时间。 如果您不想键入时间筛选器，则这样设置非常有用。

plyql -h 192.168.60.100:8082 -i P1Y -q '

**SELECT** page **as** pg,

**COUNT**\(\) **as** cnt

**FROM** wikipedia

**GROUP** **BY** page

**ORDER** **BY** cnt **DESC**

**LIMIT** 5;

'

通过TIME\_BUCKET函数可以对时间分解

plyql -h 192.168.60.100:8082 -i P1Y -q '

**SELECT** **SUM**\(added\) **as** TotalAdded

**FROM** wikipedia

**GROUP** **BY** TIME\_BUCKET\(\_\_time, PT6H, "Etc/UTC"\);

'

返回:

\[

{

"TotalAdded": 15426936,

"split0": {

"start": "2015-09-12T00:00:00.000Z",

"end": "2015-09-12T06:00:00.000Z",

"type": "TIME\_RANGE"

}

},

{

"TotalAdded": 25996165,

"split0": {

"start": "2015-09-12T06:00:00.000Z",

"end": "2015-09-12T12:00:00.000Z",

"type": "TIME\_RANGE"

}

},

"... 结果省略了 ..."

\]

注意分组列如果没有选择但仍有返回, 列如TIME\_BUCKET\(\_\_time, PT1H, 'Etc/UTC'\) as 'split' 是查询列中的一个。 时间分割也支持，这里是一个例子：

plyql -h 192.168.60.100:8082 -i P1Y -q '

**SELECT** TIME\_PART\(\_\_time, HOUR\_OF\_DAY, "Etc/UTC"\) **as** HourOfDay,

**SUM**\(added\) **as** TotalAdded

**FROM** wikipedia

**GROUP** **BY** 1

**ORDER** **BY** TotalAdded **DESC** **LIMIT** 3;

'

注意，这个 GROUP BY 指的是选择中的第一列。 这将返回：

\[

{

"TotalAdded": 8077302,

"HourOfDay": 10

},

{

"TotalAdded": 5998730,

"HourOfDay": 17

},

{

"TotalAdded": 5210222,

"HourOfDay": 18

}

\]

支持对直方图的分位数。 假设您想要使用直方图计算 0.95 分位数的三角洲筛选的城市是旧金山。

plyql -h 192.168.60.100:8082 -i P1Y -q '

**SELECT**

QUANTILE\(delta\_hist **WHERE** cityName = "San Francisco", 0.95\) **as** P95

**FROM** wikipedia;

'

它也是可能做到多维度分组查询的

plyql -h 192.168.60.100:8082 -i P1Y -q '

**SELECT** TIME\_BUCKET\(\_\_time, PT1H, "Etc/UTC"\) **as** **Hour**,

page **as** PageName,

**SUM**\(added\) **as** TotalAdded

**FROM** wikipedia

**GROUP** **BY** 1, 2

**ORDER** **BY** TotalAdded **DESC**

**LIMIT** 3;

'

返回:

\[

{

"TotalAdded": 242211,

"PageName": "Wikipedia‐ノート:即時削除の方針/過去ログ16",

"Hour": {

"start": "2015-09-12T15:00:00.000Z",

"end": "2015-09-12T16:00:00.000Z",

"type": "TIME\_RANGE"

}

},

{

"TotalAdded": 232941,

"PageName": "Користувач:SuomynonA666/Заготовка",

"Hour": {

"start": "2015-09-12T14:00:00.000Z",

"end": "2015-09-12T15:00:00.000Z",

"type": "TIME\_RANGE"

}

},

{

"TotalAdded": 214017,

"PageName": "User talk:Estela.rs",

"Hour": {

"start": "2015-09-12T12:00:00.000Z",

"end": "2015-09-12T13:00:00.000Z",

"type": "TIME\_RANGE"

}

}

\]

这里是一个高级的示例，获取前 5 页编辑时间。 PlyQL 的一个显著特征是它对待 （aka 表） 的数据集作为可以嵌套在另一个表的只是另一种数据类型。 这使我们能够嵌套查询数据像这样︰

plyql -h 192.168.60.100:8082 -i P1Y -q '

**SELECT** page **as** Page,

**COUNT**\(\) **as** cnt,

\(

**SELECT**

**SUM**\(added\) **as** TotalAdded

**GROUP** **BY** TIME\_BUCKET\(\_\_time, PT1H, "Etc/UTC"\)

**LIMIT** 3 _-- only get the first 3 hours to keep this example output small_

\) **as** "ByTime"

**FROM** wikipedia

**GROUP** **BY** page

**ORDER** **BY** cnt **DESC**

**LIMIT** 5;

'

返回:

\[

{

"cnt": 314,

"Page": "Jeremy Corbyn",

"ByTime": \[

{

"TotalAdded": 1075,

"split0": {

"start": "2015-09-12T01:00:00.000Z",

"end": "2015-09-12T02:00:00.000Z",

"type": "TIME\_RANGE"

}

},

{

"TotalAdded": 0,

"split0": {

"start": "2015-09-12T07:00:00.000Z",

"end": "2015-09-12T08:00:00.000Z",

"type": "TIME\_RANGE"

}

},

{

"TotalAdded": 10553,

"split0": {

"start": "2015-09-12T08:00:00.000Z",

"end": "2015-09-12T09:00:00.000Z",

"type": "TIME\_RANGE"

}

}

\]

},

{

"cnt": 255,

"Page": "User:Cyde/List of candidates for speedy deletion/Subpage",

"ByTime": \[

{

"TotalAdded": 73,

"split0": {

"start": "2015-09-12T00:00:00.000Z",

"end": "2015-09-12T01:00:00.000Z",

"type": "TIME\_RANGE"

}

},

{

"TotalAdded": 3363,

"split0": {

"start": "2015-09-12T01:00:00.000Z",

"end": "2015-09-12T02:00:00.000Z",

"type": "TIME\_RANGE"

}

},

{

"TotalAdded": 336,

"split0": {

"start": "2015-09-12T02:00:00.000Z",

"end": "2015-09-12T03:00:00.000Z",

"type": "TIME\_RANGE"

}

}

\]

},

"... results omitted ..."

\]
```
