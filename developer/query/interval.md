# Tindex-Query-Json `interval`属性详情如下

## `Interval` 时间区间

在查询中指定时间区间。`Interval`中的时间是`ISO-8601`格式。对于中国用户，所在时区为东8区，因此需要在时间中加入“+08:00”。 如"2015-12-31T16:00:00+08:00 / 2017-04-14T15:59:59+08:00"。
- `Interval` 类别详情如下：
  - [`Intervals`](#Intervals)
  - [`Segments`](#Segments)


### <a id="Intervals" href="Intervals"></a> 1. `Intervals Interval`
`JSON`示例如下：
```
{
    "type":"intervals",
    "intervals":[<interval>,<interval>,...]
}
```

### <a id="Segments" href="Segments"></a>2. `Segments Interval`
`Segments Interval`可以定义多个段，`JSON`示例如下：
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