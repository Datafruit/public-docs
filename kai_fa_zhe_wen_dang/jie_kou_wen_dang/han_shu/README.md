### 函数

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