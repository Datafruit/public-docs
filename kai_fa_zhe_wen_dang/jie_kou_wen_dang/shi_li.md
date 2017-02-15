### 示例

这些例子我们查询一个Druid的broker节点：192.168.60.100 端口： 8082.

首先，我们可以为数据源列表发出一个SHOW TABLES查询，我们将其传递到--query 或者 -q

MYSQL 查询Druid数据源列表

plyql -h 192.168.60.100:8082 -q &#039;**SHOW** **TABLES**&#039;

HTTP接口请求

http://192.168.60.100:8082/api/plyql/sql

post请求: application/json 参数

{

&quot;query&quot;:

&quot;SHOW TABLES&quot;

}

返回JSON:

{

&quot;result&quot;: [

{

&quot;Tables_in_database&quot;: &quot;wikipedia&quot;

},

....

]

}

查询Druid数据源表数据结构

plyql -h 192.168.60.100:8082 -q &#039;**DESCRIBE** wikipedia&#039;

返回数据源的列定义：

[

{

&quot;name&quot;: &quot;__time&quot;,

&quot;type&quot;: &quot;TIME&quot;

},

{

&quot;name&quot;: &quot;added&quot;,

&quot;type&quot;: &quot;NUMBER&quot;,

&quot;unsplitable&quot;: true

},

{

&quot;name&quot;: &quot;channel&quot;,

&quot;type&quot;: &quot;STRING&quot;

},

{

&quot;name&quot;: &quot;cityName&quot;,

&quot;type&quot;: &quot;STRING&quot;

},

{

&quot;name&quot;: &quot;comment&quot;,

&quot;type&quot;: &quot;STRING&quot;

},

{

&quot;name&quot;: &quot;commentLength&quot;,

&quot;type&quot;: &quot;STRING&quot;

},

{

&quot;name&quot;: &quot;count&quot;,

&quot;type&quot;: &quot;NUMBER&quot;,

&quot;unsplitable&quot;: true

},

{

&quot;name&quot;: &quot;countryIsoCode&quot;,

&quot;type&quot;: &quot;STRING&quot;

},

{

&quot;name&quot;: &quot;countryName&quot;,

&quot;type&quot;: &quot;STRING&quot;

},

{

&quot;name&quot;: &quot;deleted&quot;,

&quot;type&quot;: &quot;NUMBER&quot;,

&quot;unsplitable&quot;: true

},

{

&quot;name&quot;: &quot;delta&quot;,

&quot;type&quot;: &quot;NUMBER&quot;,

&quot;unsplitable&quot;: true

},

{

&quot;name&quot;: &quot;deltaByTen&quot;,

&quot;type&quot;: &quot;NUMBER&quot;,

&quot;unsplitable&quot;: true

},

{

&quot;name&quot;: &quot;delta_hist&quot;,

&quot;type&quot;: &quot;NUMBER&quot;,

&quot;special&quot;: &quot;histogram&quot;

},

&quot;... results omitted ...&quot;

]

这里是一个简单的查询，获取最大的__time信息。此查询显示数据库中最新事件的时间。

plyql -h 192.168.60.100:8082 -q &#039;**SELECT** **MAX**(__time) **AS** maxTime **FROM** wikipedia&#039;

返回:

[

{

&quot;maxTime&quot;: {

&quot;type&quot;: &quot;TIME&quot;,

&quot;value&quot;: &quot;2015-09-12T23:59:00.000Z&quot;

}

}

]

现在你可能要检查，不同的标签趋势。

您可能会在这样的页面列上做分组：

plyql -h 192.168.60.100:8082 -q &#039;

**SELECT** page **as** pg,

**COUNT**() **as** cnt

**FROM** wikipedia

**GROUP** **BY** page

**ORDER** **BY** cnt **DESC**

**LIMIT** 5;

&#039;

这将抛出一个错误，因为没有时间过滤指定plyql查询的范围。

这种行为可以禁用使用--allow eternity，但不推荐这样做，这样做时，当数据量过大时，它可以发出计算禁止查询。

再试一次，用一个时间过滤器：

plyql -h 192.168.60.100:8082 -q &#039;

**SELECT** page **as** pg,

**COUNT**() **as** cnt

**FROM** wikipedia

**WHERE** &quot;2015-09-12T00:00:00&quot; &lt;= __time **AND** __time &lt; &quot;2015-09-13T00:00:00&quot;

**GROUP** **BY** page

**ORDER** **BY** cnt **DESC**

**LIMIT** 5;

&#039;

结果集:

[

{

&quot;cnt&quot;: 314,

&quot;pg&quot;: &quot;Jeremy Corbyn&quot;

},

{

&quot;cnt&quot;: 255,

&quot;pg&quot;: &quot;User:Cyde/List of candidates for speedy deletion/Subpage&quot;

},

{

&quot;cnt&quot;: 228,

&quot;pg&quot;: &quot;Wikipedia:Administrators&#039; noticeboard/Incidents&quot;

},

{

&quot;cnt&quot;: 186,

&quot;pg&quot;: &quot;Wikipedia:Vandalismusmeldung&quot;

},

{

&quot;cnt&quot;: 160,

&quot;pg&quot;: &quot;Total Drama Presents: The Ridonculous Race&quot;

}

]

Plyql有一个选项 --interval (-i) 自动过滤器加上interval间隔时间。 如果您不想键入时间筛选器，则这样设置非常有用。

plyql -h 192.168.60.100:8082 -i P1Y -q &#039;

**SELECT** page **as** pg,

**COUNT**() **as** cnt

**FROM** wikipedia

**GROUP** **BY** page

**ORDER** **BY** cnt **DESC**

**LIMIT** 5;

&#039;

通过TIME_BUCKET函数可以对时间分解

plyql -h 192.168.60.100:8082 -i P1Y -q &#039;

**SELECT** **SUM**(added) **as** TotalAdded

**FROM** wikipedia

**GROUP** **BY** TIME_BUCKET(__time, PT6H, &quot;Etc/UTC&quot;);

&#039;

返回:

[

{

&quot;TotalAdded&quot;: 15426936,

&quot;split0&quot;: {

&quot;start&quot;: &quot;2015-09-12T00:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T06:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

},

{

&quot;TotalAdded&quot;: 25996165,

&quot;split0&quot;: {

&quot;start&quot;: &quot;2015-09-12T06:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T12:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

},

&quot;... 结果省略了 ...&quot;

]

注意分组列如果没有选择但仍有返回, 列如TIME_BUCKET(__time, PT1H, &#039;Etc/UTC&#039;) as &#039;split&#039; 是查询列中的一个。 时间分割也支持，这里是一个例子：

plyql -h 192.168.60.100:8082 -i P1Y -q &#039;

**SELECT** TIME_PART(__time, HOUR_OF_DAY, &quot;Etc/UTC&quot;) **as** HourOfDay,

**SUM**(added) **as** TotalAdded

**FROM** wikipedia

**GROUP** **BY** 1

**ORDER** **BY** TotalAdded **DESC** **LIMIT** 3;

&#039;

注意，这个 GROUP BY 指的是选择中的第一列。 这将返回：

[

{

&quot;TotalAdded&quot;: 8077302,

&quot;HourOfDay&quot;: 10

},

{

&quot;TotalAdded&quot;: 5998730,

&quot;HourOfDay&quot;: 17

},

{

&quot;TotalAdded&quot;: 5210222,

&quot;HourOfDay&quot;: 18

}

]

支持对直方图的分位数。 假设您想要使用直方图计算 0.95 分位数的三角洲筛选的城市是旧金山。

plyql -h 192.168.60.100:8082 -i P1Y -q &#039;

**SELECT**

QUANTILE(delta_hist **WHERE** cityName = &quot;San Francisco&quot;, 0.95) **as** P95

**FROM** wikipedia;

&#039;

它也是可能做到多维度分组查询的

plyql -h 192.168.60.100:8082 -i P1Y -q &#039;

**SELECT** TIME_BUCKET(__time, PT1H, &quot;Etc/UTC&quot;) **as** **Hour**,

page **as** PageName,

**SUM**(added) **as** TotalAdded

**FROM** wikipedia

**GROUP** **BY** 1, 2

**ORDER** **BY** TotalAdded **DESC**

**LIMIT** 3;

&#039;

返回:

[

{

&quot;TotalAdded&quot;: 242211,

&quot;PageName&quot;: &quot;Wikipedia‐ノート:即時削除の方針/過去ログ16&quot;,

&quot;Hour&quot;: {

&quot;start&quot;: &quot;2015-09-12T15:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T16:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

},

{

&quot;TotalAdded&quot;: 232941,

&quot;PageName&quot;: &quot;Користувач:SuomynonA666/Заготовка&quot;,

&quot;Hour&quot;: {

&quot;start&quot;: &quot;2015-09-12T14:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T15:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

},

{

&quot;TotalAdded&quot;: 214017,

&quot;PageName&quot;: &quot;User talk:Estela.rs&quot;,

&quot;Hour&quot;: {

&quot;start&quot;: &quot;2015-09-12T12:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T13:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

}

]

这里是一个高级的示例，获取前 5 页编辑时间。 PlyQL 的一个显著特征是它对待 （aka 表） 的数据集作为可以嵌套在另一个表的只是另一种数据类型。 这使我们能够嵌套查询数据像这样︰

plyql -h 192.168.60.100:8082 -i P1Y -q &#039;

**SELECT** page **as** Page,

**COUNT**() **as** cnt,

(

**SELECT**

**SUM**(added) **as** TotalAdded

**GROUP** **BY** TIME_BUCKET(__time, PT1H, &quot;Etc/UTC&quot;)

**LIMIT** 3 _-- only get the first 3 hours to keep this example output small_

) **as** &quot;ByTime&quot;

**FROM** wikipedia

**GROUP** **BY** page

**ORDER** **BY** cnt **DESC**

**LIMIT** 5;

&#039;

返回:

[

{

&quot;cnt&quot;: 314,

&quot;Page&quot;: &quot;Jeremy Corbyn&quot;,

&quot;ByTime&quot;: [

{

&quot;TotalAdded&quot;: 1075,

&quot;split0&quot;: {

&quot;start&quot;: &quot;2015-09-12T01:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T02:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

},

{

&quot;TotalAdded&quot;: 0,

&quot;split0&quot;: {

&quot;start&quot;: &quot;2015-09-12T07:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T08:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

},

{

&quot;TotalAdded&quot;: 10553,

&quot;split0&quot;: {

&quot;start&quot;: &quot;2015-09-12T08:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T09:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

}

]

},

{

&quot;cnt&quot;: 255,

&quot;Page&quot;: &quot;User:Cyde/List of candidates for speedy deletion/Subpage&quot;,

&quot;ByTime&quot;: [

{

&quot;TotalAdded&quot;: 73,

&quot;split0&quot;: {

&quot;start&quot;: &quot;2015-09-12T00:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T01:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

},

{

&quot;TotalAdded&quot;: 3363,

&quot;split0&quot;: {

&quot;start&quot;: &quot;2015-09-12T01:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T02:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

},

{

&quot;TotalAdded&quot;: 336,

&quot;split0&quot;: {

&quot;start&quot;: &quot;2015-09-12T02:00:00.000Z&quot;,

&quot;end&quot;: &quot;2015-09-12T03:00:00.000Z&quot;,

&quot;type&quot;: &quot;TIME_RANGE&quot;

}

}

]

},

&quot;... results omitted ...&quot;

]