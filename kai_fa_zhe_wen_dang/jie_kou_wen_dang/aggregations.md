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