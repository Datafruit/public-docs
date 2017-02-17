# 函数

&lt;a id="TIME\_BUCKET" href="\#TIME\_BUCKET"&gt;\#&lt;/a&gt; **TIME\_BUCKET**\(operand, duration, timezone\)

将时间在给定的timezone\(时区\)中按给定的duration\(粒度\)查询

示例: TIME\_BUCKET\(time, 'P1D', 'America/Los\_Angeles'\)

这将把time变量装入日期块，其中按America/Los\_Angeles时区的天粒度定义。

&lt;a id="TIME\_PART" href="\#TIME\_PART"&gt;\#&lt;/a&gt; **TIME\_PART**\(operand, part, timezone\)

按指定的时区获取对应参数的时间参数值

例如: TIME\_PART\(time, 'DAY\_OF\_YEAR', 'America/Los\_Angeles'\) 这将把'time\`变量分成（整数）数字，表示一年中的哪一天。 可能的部分值是:

* SECOND\_OF\_MINUTE, SECOND\_OF\_HOUR, SECOND\_OF\_DAY, SECOND\_OF\_WEEK, SECOND\_OF\_MONTH, SECOND\_OF\_YEAR
* MINUTE\_OF\_HOUR, MINUTE\_OF\_DAY, MINUTE\_OF\_WEEK, MINUTE\_OF\_MONTH, MINUTE\_OF\_YEAR
* HOUR\_OF\_DAY, HOUR\_OF\_WEEK, HOUR\_OF\_MONTH, HOUR\_OF\_YEAR
* DAY\_OF\_WEEK, DAY\_OF\_MONTH, DAY\_OF\_YEAR
* WEEK\_OF\_MONTH, WEEK\_OF\_YEAR
* MONTH\_OF\_YEAR
* YEAR

```javascript


&lt;a id="TIME\_FLOOR" href="\#TIME\_FLOOR"&gt;\#&lt;/a&gt; **TIME\_FLOOR**\(operand, duration, timezone\)

在给定的timezone\(时区\)中将时间置于最近的duration\(粒度\)。 示例: TIME\_FLOOR\(time, 'P1D', 'America/Los\_Angeles'\)

这将把time变量置于一天的开始，其中天在America/Los\_Angeles时区中定义。

&lt;a id="TIME\_SHIFT" href="\#TIME\_SHIFT"&gt;\#&lt;/a&gt; **TIME\_SHIFT**\(operand, duration, step, timezone\)

在给定的timezone\(时区\)中，通过duration\(粒度\) \* step向前移动时间。 step可能是负数。

示例: TIME\_SHIFT\(time, 'P1D', -2, 'America/Los\_Angeles'\)

这将在两天后移动time变量，其中天在America/Los\_Angeles时区中定义。

&lt;a id="TIME\_RANGE" href="\#TIME\_RANGE"&gt;\#&lt;/a&gt; **TIME\_RANGE**\(operand, duration, step, timezone\)

在给定的timezone\(时区\)中创建一个范围形式time和一个duration \*step远离time的点。 step可能是负数。

示例: TIME\_RANGE\(time, 'P1D', -2, 'America/Los\_Angeles'\)

这将在两天后移动time变量，其中天在America/Los\_Angeles时区中定义，并创建一个时间-2 \* P1D - &gt;时间范围。

&lt;a id="SUBSTR" href="\#SUBSTR"&gt;\#&lt;/a&gt; **SUBSTR**\(str, pos, len\)

从字符串str返回一个子串len个字符，从位置pos开始

&lt;a id="CONCAT" href="\#CONCAT"&gt;\#&lt;/a&gt; **CONCAT**\(str1, str2, ...\)

返回由连接参数产生的字符串。 可以有一个或多个参数

&lt;a id="EXTRACT" href="\#EXTRACT"&gt;\#&lt;/a&gt; **EXTRACT**\(str, regexp\)

返回第一个匹配的组，结果窗体匹配 regexp 到 str

&lt;a id="LOOKUP" href="\#LOOKUP"&gt;\#&lt;/a&gt; **LOOKUP**\(str, lookup-namespace\)

返回键的值 str 内 查找-命名空间

&lt;a id="IFNULL" href="\#IFNULL"&gt;\#&lt;/a&gt; **IFNULL**\(expr1, expr2\)

返回 expr 如果不为空，否则返回 expr2

&lt;a id="FALLBACK" href="\#FALLBACK"&gt;\#&lt;/a&gt; **FALLBACK**\(expr1, expr2\)

这个等同于 **IFNULL**\(expr1, expr2\)

&lt;a id="OVERLAP" href="\#OVERLAP"&gt;\#&lt;/a&gt; **OVERLAP**\(expr1, expr2\)

检查 expr1 和 expr2 是否重叠

```