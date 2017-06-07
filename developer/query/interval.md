# Tindex-Query-Json `interval`属性详情如下

## Interval 时间区间

&#160; &#160; &#160; &#160;在查询中指定时间区间。Interval中的时间是ISO-8601格式。对于中国用户，所在时区为东8区，因此需要在时间中加入“+08:00”。 如"2015-12-31T16:00:00+08:00 / 2017-04-14T15:59:59+08:00"。


### 1. Intervals Interval
&#160; &#160; &#160; &#160;JSON示例如下：
```
{
    "type":"intervals",
    "intervals":[<interval>,<interval>,...]
}
```

### 2. Segments Interval
&#160; &#160; &#160; &#160;Segments Interval 可以定义多个段，JSON示例如下：
```
{
    "type":"segments",
    "segments":[
    	{
            "itvl":{<interval>},
            "ver":"version",
            "part":100
        },
        {
            "itvl":{<interval>},
            "ver":"version",
            "part":200
        }
    ]
}
```