## 函数

<a id="TIME_BUCKET" href="#TIME_BUCKET">#</a>**TIME_BUCKET**(operand, duration, timezone)


将时间在给定的`timezone(时区)`中按给定的`duration(粒度)`查询

示例: `TIME_BUCKET(time, 'P1D', 'America/Los_Angeles')`

这将把`time`变量装入日期块，其中按`America/Los_Angeles`时区的天粒度定义。

<a id="TIME_PART" href="#TIME_PART">#</a>**TIME_PART**(operand, part, timezone)

按指定的时区获取对应参数的时间参数值

例如: `TIME_PART(time, 'DAY_OF_YEAR', 'America/Los_Angeles')`
这将把'time`变量分成（整数）数字，表示一年中的哪一天。
可能的部分值是:

* `SECOND_OF_MINUTE`, `SECOND_OF_HOUR`, `SECOND_OF_DAY`, `SECOND_OF_WEEK`, `SECOND_OF_MONTH`, `SECOND_OF_YEAR`
* `MINUTE_OF_HOUR`, `MINUTE_OF_DAY`, `MINUTE_OF_WEEK`, `MINUTE_OF_MONTH`, `MINUTE_OF_YEAR`
* `HOUR_OF_DAY`, `HOUR_OF_WEEK`, `HOUR_OF_MONTH`, `HOUR_OF_YEAR`
* `DAY_OF_WEEK`, `DAY_OF_MONTH`, `DAY_OF_YEAR`
* `WEEK_OF_MONTH`, `WEEK_OF_YEAR`
* `MONTH_OF_YEAR`
* `YEAR`


<a id="TIME_FLOOR" href="#TIME_FLOOR">#</a>**TIME_FLOOR**(operand, duration, timezone)

在给定的`timezone(时区)`中将时间置于最近的`duration(粒度)`。
示例: `TIME_FLOOR(time, 'P1D', 'America/Los_Angeles')`

这将把`time`变量置于一天的开始，其中天在`America/Los_Angeles`时区中定义。

<a id="TIME_SHIFT" href="#TIME_SHIFT">#</a>**TIME_SHIFT**(operand, duration, step, timezone)

在给定的`timezone(时区)`中，通过`duration(粒度)` * `step`向前移动时间。
`step`可能是负数。

示例: `TIME_SHIFT(time, 'P1D', -2, 'America/Los_Angeles')`

这将在两天后移动`time`变量，其中天在`America/Los_Angeles`时区中定义。

<a id="TIME_RANGE" href="#TIME_RANGE">#</a>**TIME_RANGE**(operand, duration, step, timezone)

在给定的`timezone(时区)`中创建一个范围形式`time`和一个`duration` *`step`远离`time`的点。
`step`可能是负数。

示例: `TIME_RANGE(time, 'P1D', -2, 'America/Los_Angeles')`

这将在两天后移动`time`变量，其中天在`America/Los_Angeles`时区中定义，并创建一个时间-2 * P1D - >时间范围。

<a id="SUBSTR" href="#SUBSTR">#</a>**SUBSTR**(*str*, *pos*, *len*)

从字符串*str*返回一个子串*len*个字符，从位置*pos*开始

<a id="CONCAT" href="#CONCAT">#</a>
**CONCAT**(*str1*, *str2*, ...)

返回由连接参数产生的字符串。 可以有一个或多个参数

<a id="EXTRACT" href="#EXTRACT">#</a>**EXTRACT**(*str*, *regexp*)

返回第一个匹配的组，结果窗体匹配 *regexp* 到 *str*

<a id="LOOKUP" href="#LOOKUP">#</a>**LOOKUP**(*str*, *lookup-namespace*)

返回键的值 *str* 内 *查找-命名空间*

<a id="IFNULL" href="#IFNULL">#</a>**IFNULL**(*expr1*, *expr2*)

返回 *expr* 如果不为空，否则返回 *expr2*

<a id="FALLBACK" href="#FALLBACK">#</a>**FALLBACK**(*expr1*, *expr2*)

这个等同于 **IFNULL**(*expr1*, *expr2*)

<a id="OVERLAP" href="#OVERLAP">#</a>**OVERLAP**(*expr1*, *expr2*)

检查 *expr1* 和 *expr2* 是否重叠

### 数学函数

<a id="ABS" href="#ABS">#</a>**ABS**(*expr*)

返回*expr*的绝对值。

<a id="POW" href="#POW">#</a>**POW**(*expr1*, *expr2*)

返回 *expr1* 的幂 *expr2*。

<a id="POWER" href="#POWER">#</a>**POWER**(*expr1*, *expr2*)  

这个等同于 **POW**(*expr1*, *expr2*)  

<a id="SQRT" href="#SQRT">#</a>**SQRT**(*expr*)

返回*expr*的平方根

<a id="EXP" href="#EXP">#</a>  **EXP**(*expr*)

返回 e（自然对数的基数） 的值*expr*的幂。

## 聚合函数

<a id="COUNT" href="#COUNT">#</a>**COUNT**(*expr?*)

如果在没有表达式的情况下使用（或者作为`COUNT(*)`）返回行数的计数。
当提供表达式时，返回*expr*不为null的行的计数。

<a id="COUNT" href="#COUNT">#</a>**COUNT**(**DISTINCT** *expr*), **COUNT_DISTINCT**(*expr*) 

返回具有不同*expr*值的行数的计数。

<a id="SUM" href="#SUM">#</a>**SUM**(*expr*) 

返回所有*expr*值的总和。

<a id="MIN" href="#MIN">#</a>**MIN**(*expr*) 

返回所有*expr*值的最小值。

<a id="MAX" href="#MAX">#</a>**MAX**(*expr*) 

返回所有*expr*值的最大值。

<a id="AVG" href="#AVG">#</a>**AVG**(*expr*)

返回所有*expr*值的平均值。

<a id="QUANTILE" href="#QUANTILE">#</a>**QUANTILE**(*expr*, *quantile*)

返回所有*expr*值的上层*分位数*。

<a id="CUSTOM" href="#CUSTOM">#</a>**CUSTOM**(*custom_name*)

返回名为*custom_name*的用户定义聚合。