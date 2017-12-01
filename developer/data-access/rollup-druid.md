>本例展示如何对数据进行预聚合接入

- 本例使用的json如下:
```json
{
	"type": "lucene_supervisor",
	"dataSchema": {
		"dataSource": "rollup-test",
		"parser":{
			"type":"string",
			"parseSpec":{
				"format":"json",
				"dimensionsSpec":{
					"dynamicDimension":false,   //预聚合不能使用动态维
					"dimensions":[
						{"name":"s|province","type":"string"},
						{"name":"s|event","type":"string"}
					]
				},
				"timestampSpec":{
					"column":"d|sugo_time",
					"excludeTimeColumn": false,
					"format":"millis"
				}
			}
		},
		"metricsSpec": [{
			"type": "thetaSketch",
			"name": "uid_estimated_count",
			"fieldName": "s|uid"      //指定预聚合的维度(用于统计基数), 应该注意这个维度不应该再出现dimensionsSpec中,所以也就不能用动态维
		}],
		"granularitySpec": {
			"type": "uniform",
			"segmentGranularity": "DAY",
			"queryGranularity":  {    
				"type":"period",
				"period":"P1D"     //指定预聚合的粒度,不可留空,一定要设置
			},
			"rollup": true,      //设为true表示进行预聚合
			"intervals": null
		}
	},
	"tuningConfig": {
		"type":"kafka",
		"maxRowsInMemory":10000000,
		"maxRowsPerSegment":20000000,
		"intermediatePersistPeriod":"PT10M",
		"buildV9Directly":true,
		"reportParseExceptions":true
	},
	"ioConfig": {
		"topic": "rollup_test",
		"replicas": 1,
		"taskCount": 1,
		"taskDuration": "PT300S",
		"consumerProperties": {
			"bootstrap.servers": "192.168.0.220:9092,192.168.0.221:9092,192.168.0.222:9092"
		},
		"startDelay": "PT5S",
		"period": "PT30S",
		"useEarliestOffset": true,
		"completionTimeout": "PT1800S",
		"lateMessageRejectionPeriod": null
	}
}
```