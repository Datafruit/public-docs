# Aggregations

```javascript

&lt;a id="COUNT" href="\#COUNT"&gt;\#&lt;/a&gt; **COUNT**\(expr?\)

如果在没有表达式的情况下使用（或者作为COUNT\(\*\)）返回行数的计数。 当提供表达式时，返回expr不为null的行的计数。

&lt;a id="COUNT" href="\#COUNT"&gt;\#&lt;/a&gt; **COUNT**\(**DISTINCT** expr\), **COUNT\_DISTINCT**\(expr\)

返回具有不同expr值的行数的计数。

&lt;a id="SUM" href="\#SUM"&gt;\#&lt;/a&gt; **SUM**\(expr\)

返回所有expr值的总和。

&lt;a id="MIN" href="\#MIN"&gt;\#&lt;/a&gt; **MIN**\(expr\)

返回所有expr值的最小值。

&lt;a id="MAX" href="\#MAX"&gt;\#&lt;/a&gt; **MAX**\(expr\)

返回所有expr值的最大值。

&lt;a id="AVG" href="\#AVG"&gt;\#&lt;/a&gt; **AVG**\(expr\)

返回所有expr值的平均值。

&lt;a id="QUANTILE" href="\#QUANTILE"&gt;\#&lt;/a&gt; **QUANTILE**\(expr, quantile\)

返回所有expr值的上层分位数。

&lt;a id="CUSTOM" href="\#CUSTOM"&gt;\#&lt;/a&gt; **CUSTOM**\(custom\_name\)

返回名为custom\_name的用户定义聚合。
```
