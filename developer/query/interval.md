## 区间

intervals.type可选项： intervals , segments , 也可以是一个字符串，比如"2015-12-31T16:00:00.000Z/2017-04-14T15:59:59.999Z"

### intervals
intervals.type=intervals时，参数：
```
{
	"type":"intervals",
	"intervals":[
    	    <interval>,<interval>,...
	]
}
```
- intervals:可以定义多个区间


### 段
intervals.type=segments时，参数：
```
{
    "type":"segments",
    "segments":[
    	{
        	"itvl":{
			<interval>
		},
		"ver":"<version>",
		"part":100
	},
	{
		"itvl":{
			<interval>
		},
		"ver":"<version>",
		"part":200
	},...
    ]
}
```
可定义多个段

