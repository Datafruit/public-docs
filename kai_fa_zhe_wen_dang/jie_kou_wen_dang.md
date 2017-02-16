## 接口文档

#Sugo PlyQL

plyql是类似SQL的语言，可以解析成sugo-plywood的表达并执行。

plyql只支持SELECT，DESCRIBE，和SHOW TABLES查询。

### 示例 {#-0}

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

### 运算符 {#-1}

| **名称** | **描述** |
| --- | --- |
| + | 加法运算符 |
| - | 减号运算符 / 一元求反 |
| * | 乘法运算符 |
| / | 除法运算符 |
| =, IS | 等于运算符 |
| !=, &lt;&gt;, IS NOT | 不等于运算符 |
| &gt;= | 大于或等于运算符 |
| &gt; | 大于运算符 |
| &lt;= | 小于或等于运算符 |
| &lt; | 小于运算符 |
| BETWEEN ... AND ... | 检查一个值是否在值范围内 |
| NOT BETWEEN ... AND ... | 检查一个值是否不在值范围内 |
| LIKE, CONTAINS | 简单的模式(模糊)匹配 |
| NOT LIKE, CONTAINS | 否定的简单模式(模糊)匹配 |
| REGEXP | 模式匹配使用正则表达式 |
| NOT REGEXP | 正则表达式的否定 |
| NOT, ! | 取否 |
| AND | 逻辑并 |
| OR | 逻辑或 |

### 函数 {#-2}

&lt;a id=&quot;TIME_BUCKET&quot; href=&quot;#TIME_BUCKET&quot;&gt;#&lt;/a&gt; **TIME_BUCKET**(operand, duration, timezone)

将时间在给定的timezone(时区)中按给定的duration(粒度)查询

示例: TIME_BUCKET(time, &#039;P1D&#039;, &#039;America/Los_Angeles&#039;)

这将把time变量装入日期块，其中按America/Los_Angeles时区的天粒度定义。

&lt;a id=&quot;TIME_PART&quot; href=&quot;#TIME_PART&quot;&gt;#&lt;/a&gt; **TIME_PART**(operand, part, timezone)

按指定的时区获取对应参数的时间参数值

例如: TIME_PART(time, &#039;DAY_OF_YEAR&#039;, &#039;America/Los_Angeles&#039;) 这将把&#039;time`变量分成（整数）数字，表示一年中的哪一天。 可能的部分值是:

*   SECOND_OF_MINUTE, SECOND_OF_HOUR, SECOND_OF_DAY, SECOND_OF_WEEK, SECOND_OF_MONTH, SECOND_OF_YEAR
*   MINUTE_OF_HOUR, MINUTE_OF_DAY, MINUTE_OF_WEEK, MINUTE_OF_MONTH, MINUTE_OF_YEAR
*   HOUR_OF_DAY, HOUR_OF_WEEK, HOUR_OF_MONTH, HOUR_OF_YEAR
*   DAY_OF_WEEK, DAY_OF_MONTH, DAY_OF_YEAR
*   WEEK_OF_MONTH, WEEK_OF_YEAR
*   MONTH_OF_YEAR
*   YEAR

&lt;a id=&quot;TIME_FLOOR&quot; href=&quot;#TIME_FLOOR&quot;&gt;#&lt;/a&gt; **TIME_FLOOR**(operand, duration, timezone)

在给定的timezone(时区)中将时间置于最近的duration(粒度)。 示例: TIME_FLOOR(time, &#039;P1D&#039;, &#039;America/Los_Angeles&#039;)

这将把time变量置于一天的开始，其中天在America/Los_Angeles时区中定义。

&lt;a id=&quot;TIME_SHIFT&quot; href=&quot;#TIME_SHIFT&quot;&gt;#&lt;/a&gt; **TIME_SHIFT**(operand, duration, step, timezone)

在给定的timezone(时区)中，通过duration(粒度) * step向前移动时间。 step可能是负数。

示例: TIME_SHIFT(time, &#039;P1D&#039;, -2, &#039;America/Los_Angeles&#039;)

这将在两天后移动time变量，其中天在America/Los_Angeles时区中定义。

&lt;a id=&quot;TIME_RANGE&quot; href=&quot;#TIME_RANGE&quot;&gt;#&lt;/a&gt; **TIME_RANGE**(operand, duration, step, timezone)

在给定的timezone(时区)中创建一个范围形式time和一个duration *step远离time的点。 step可能是负数。

示例: TIME_RANGE(time, &#039;P1D&#039;, -2, &#039;America/Los_Angeles&#039;)

这将在两天后移动time变量，其中天在America/Los_Angeles时区中定义，并创建一个时间-2 * P1D - &gt;时间范围。

&lt;a id=&quot;SUBSTR&quot; href=&quot;#SUBSTR&quot;&gt;#&lt;/a&gt; **SUBSTR**(str, pos, len)

从字符串str返回一个子串len个字符，从位置pos开始

&lt;a id=&quot;CONCAT&quot; href=&quot;#CONCAT&quot;&gt;#&lt;/a&gt; **CONCAT**(str1, str2, ...)

返回由连接参数产生的字符串。 可以有一个或多个参数

&lt;a id=&quot;EXTRACT&quot; href=&quot;#EXTRACT&quot;&gt;#&lt;/a&gt; **EXTRACT**(str, regexp)

返回第一个匹配的组，结果窗体匹配 regexp 到 str

&lt;a id=&quot;LOOKUP&quot; href=&quot;#LOOKUP&quot;&gt;#&lt;/a&gt; **LOOKUP**(str, lookup-namespace)

返回键的值 str 内 查找-命名空间

&lt;a id=&quot;IFNULL&quot; href=&quot;#IFNULL&quot;&gt;#&lt;/a&gt; **IFNULL**(expr1, expr2)

返回 expr 如果不为空，否则返回 expr2

&lt;a id=&quot;FALLBACK&quot; href=&quot;#FALLBACK&quot;&gt;#&lt;/a&gt; **FALLBACK**(expr1, expr2)

这个等同于 **IFNULL**(expr1, expr2)

&lt;a id=&quot;OVERLAP&quot; href=&quot;#OVERLAP&quot;&gt;#&lt;/a&gt; **OVERLAP**(expr1, expr2)

检查 expr1 和 expr2 是否重叠

#### Mathematical Functions {#mathematical-functions}

&lt;a id=&quot;ABS&quot; href=&quot;#ABS&quot;&gt;#&lt;/a&gt; **ABS**(expr)

返回expr的绝对值。

&lt;a id=&quot;POW&quot; href=&quot;#POW&quot;&gt;#&lt;/a&gt; **POW**(expr1, expr2)

返回 expr1 的幂 expr2。

&lt;a id=&quot;POWER&quot; href=&quot;#POWER&quot;&gt;#&lt;/a&gt; **POWER**(expr1, expr2)

这个等同于 **POW**(expr1, expr2)

&lt;a id=&quot;SQRT&quot; href=&quot;#SQRT&quot;&gt;#&lt;/a&gt; **SQRT**(expr)

返回expr的平方根

&lt;a id=&quot;EXP&quot; href=&quot;#EXP&quot;&gt;#&lt;/a&gt;

**EXP**(expr)

返回 e（自然对数的基数） 的值expr的幂。

### Aggregations {#aggregations}

&lt;a id=&quot;COUNT&quot; href=&quot;#COUNT&quot;&gt;#&lt;/a&gt; **COUNT**(expr?)

如果在没有表达式的情况下使用（或者作为COUNT(*)）返回行数的计数。 当提供表达式时，返回expr不为null的行的计数。

&lt;a id=&quot;COUNT&quot; href=&quot;#COUNT&quot;&gt;#&lt;/a&gt; **COUNT**(**DISTINCT** expr), **COUNT_DISTINCT**(expr)

返回具有不同expr值的行数的计数。

&lt;a id=&quot;SUM&quot; href=&quot;#SUM&quot;&gt;#&lt;/a&gt; **SUM**(expr)

返回所有expr值的总和。

&lt;a id=&quot;MIN&quot; href=&quot;#MIN&quot;&gt;#&lt;/a&gt; **MIN**(expr)

返回所有expr值的最小值。

&lt;a id=&quot;MAX&quot; href=&quot;#MAX&quot;&gt;#&lt;/a&gt; **MAX**(expr)

返回所有expr值的最大值。

&lt;a id=&quot;AVG&quot; href=&quot;#AVG&quot;&gt;#&lt;/a&gt; **AVG**(expr)

返回所有expr值的平均值。

&lt;a id=&quot;QUANTILE&quot; href=&quot;#QUANTILE&quot;&gt;#&lt;/a&gt; **QUANTILE**(expr, quantile)

返回所有expr值的上层分位数。

&lt;a id=&quot;CUSTOM&quot; href=&quot;#CUSTOM&quot;&gt;#&lt;/a&gt; **CUSTOM**(custom_name)

返回名为custom_name的用户定义聚合。