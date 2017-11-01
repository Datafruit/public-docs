# suogo-druid 资源接口说明

## 1. ` OverlordResource`
> class : `io.druid.indexing.overlord.http.OverlordResource`

- `/druid/indexer/v1/task`   
**基本信息**  
接口说明: 根据发送的json语句启动任务   
请求方式:POST  
请求地址:`/druid/indexer/v1/task`  
响应类型:application/json  
数据类型:application/json  
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    {Body数据} | 是 | json_string | task任务的json字符串 |
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |
请求示例:
```
type:post
url:http://192.168.0.212:8090/druid/indexer/v1/task
Body数据:
{
  "type":"lucene_merge",
  "dataSource": "test061901",  
  "interval": "2017-06-01/2017-06-30",
  "triggerMergeCount": 2,
  "mergeGranularity":"DAY",
  "context": {
    "debug": true
  }   
}
```
结果示例:
```json
{"task":"merge_test061901_d03788d41557444bdd81d15a8b80a6d7f72cb9f8"}
```

- `/druid/indexer/v1/leader`  
**基本信息**   
接口说明:查看Overlord的leader  
请求方式:GET  
请求地址:`/druid/indexer/v1/leader`  
响应类型:application/json  
数据类型:无   
请求参数:无  

请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/leader
```
结果示例:
```
192.168.0.212:8090
```

- `/druid/indexer/v1/task/{taskid}`  
**基本信息**   
接口说明:获取指定task的json说明   
请求方式:GET  
请求地址:`/druid/indexer/v1/task/{taskid}`  
响应类型:application/json  
数据类型:无   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    taskid | 是 | string | 任务标识 |

请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/task/merge_test061901_d03788d41557444bdd81d15a8b80a6d7f72cb9f8
```
结果示例:
```json
{
    "task": "merge_test061901_d03788d41557444bdd81d15a8b80a6d7f72cb9f8",
    "payload": {
        "id": "merge_test061901_d03788d41557444bdd81d15a8b80a6d7f72cb9f8",
        "dataSource": "test061901",
        "interval": "2017-06-01T00:00:00.000Z/2017-06-30T00:00:00.000Z",
        "mergeGranularity": "DAY",
        "maxSizePerSegment": 2147483647,
        "context": {
            "debug": true
        },
        "groupId": "merge_test061901_d03788d41557444bdd81d15a8b80a6d7f72cb9f8",
        "segments": null,
        "resource": {
            "availabilityGroup": "merge_test061901_d03788d41557444bdd81d15a8b80a6d7f72cb9f8",
            "requiredCapacity": 1
        }
    }
}
```


- `/druid/indexer/v1/task/{taskid}/status`  
**基本信息**   
接口说明:获取指定task的运行状态  
请求方式:GET  
请求地址:`/druid/indexer/v1/task/{taskid}/status`  
响应类型:application/json  
数据类型:无   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    taskid | 是 | string | 任务标识 |  
请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/task/lucene_index_kafka_hll-2_80677fd2015c246_pgnbkepi/status
```
结果示例:
```json
{"task":"lucene_index_kafka_hll-2_80677fd2015c246_pgnbkepi","status":{"id":"lucene_index_kafka_hll-2_80677fd2015c246_pgnbkepi","status":"SUCCESS","duration":366175}}
```
    

- `/druid/indexer/v1/task/{taskid}/segments`  
**基本信息**   
接口说明:获取指定task对应的数据段
请求方式:GET  
请求地址:`/druid/indexer/v1/task/{taskid}/segments`  
响应类型:application/json  
数据类型:无   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    taskid | 是 | string | 任务标识 |
请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/task/lucene_index_kafka_hll-2_80677fd2015c246_pgnbkepi/segments
```
结果示例:
```json
[]
```

- `/druid/indexer/v1/task/{taskid}/shutdown`  
**基本信息**   
接口说明:关闭指定的task任务  
请求方式:POST  
请求地址:`/druid/indexer/v1/task/{taskid}/shutdown`  
响应类型:application/json  
数据类型:无   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    taskid | 是 | string | 任务标识 |
请求示例:
```
type:post
url:http://192.168.0.212:8090/druid/indexer/v1/task/lucene_index_kafka_com_SJLnjowGe_project_H1ejJETYZ_5c415123134031b_eagjpdkc/shutdown
```
结果示例:
```JSON
{
    "task": "lucene_index_kafka_com_SJLnjowGe_project_H1ejJETYZ_5c415123134031b_eagjpdkc"
}
```

- `/druid/indexer/v1/worker`  
**基本信息**   
接口说明:获取worker行为信息  
请求方式:GET  
请求地址:`/druid/indexer/v1/worker`  
响应类型:application/json  
数据类型:无   
请求参数:无  

请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/worker
```
结果示例:
```JSON
{
    "selectStrategy": {
        "type": "equalDistribution"
    },
    "autoScaler": null
}
```

- `/druid/indexer/v1/worker`   
**基本信息**  
接口说明: 设置worker授权信息   
请求方式:POST  
请求地址:`/druid/indexer/v1/worker`  
响应类型:application/json  
数据类型:application/json  
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    X-Druid-Author{HeaderParam} | 否 | string | 说明设置者是谁 | ""
    X-Druid-Comment{HeaderParam} | 否 | string | 对此次设置进行说明 | ""
请求示例:
```
type:post
url:http://192.168.0.212:8090/druid/indexer/v1/worker
Body数据:
{
    "selectStrategy": {
    	"type":"equalDistribution"
    },
    "autoScaler":null
}
```
结果示例:
```JSON
```

- `/druid/indexer/v1/worker/history`  
**基本信息**   
接口说明:获取worker历史信息   
请求方式:GET  
请求地址:`/druid/indexer/v1/worker/history`  
响应类型:application/json  
数据类型:\*\/\*   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    interval | 否 | string | 查询的历史区间 | 
    count | 否 | int | 要获取的结果数 | 

请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/worker/history?count=1
```
结果示例:
```JSON
[
    {
        "key": "worker.config",
        "type": "worker.config",
        "auditInfo": {
            "author": "",
            "comment": "",
            "ip": "192.168.0.25"
        },
        "payload": "{\"selectStrategy\":{\"type\":\"equalDistribution\"},\"autoScaler\":null}",
        "auditTime": "2017-09-08T02:10:10.919Z"
    }
]
```

- `/druid/indexer/v1/action`   
**基本信息**  
接口说明: 对某个task进行指定的操作(动作);如更新segment元信息,释放锁等操作   
请求方式:POST  
请求地址:`/druid/indexer/v1/action`  
响应类型:application/json  
数据类型:application/json  
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    {Body数据} | 是 | json_string | 指定task以及相应的action | 
请求示例:
```
type:post
url:http://192.168.0.212:8090/druid/indexer/v1/action
Body数据:
{
   "task":{
   		"type":"noop"
   },
   "action":{
   		"type":"lockList"
   }
}
```
结果示例:
```JSON
{
    "result": []
}
```

- `/druid/indexer/v1/waitingTasks`  
**基本信息**   
接口说明:获取正在等待锁的任务列表   
请求方式:GET  
请求地址:`/druid/indexer/v1/waitingTasks`  
响应类型:application/json  
数据类型:\*\/\*   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |
请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/waitingTasks
```
结果示例:
```JSON
[]
```

    
- `/druid/indexer/v1/pendingTasks`  
**基本信息**   
接口说明:获取正在等待分配给worker的任务列表   
请求方式:GET  
请求地址:`/druid/indexer/v1/pendingTasks`  
响应类型:application/json  
数据类型:\*\/\*   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |
请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/pendingTasks
```
结果示例:
```JSON
[]
```

- `/druid/indexer/v1/runningTasks`  
**基本信息**   
接口说明:获取正在运行的任务列表   
请求方式:GET  
请求地址:`/druid/indexer/v1/runningTasks`  
响应类型:application/json  
数据类型:\*\/\*   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |
请求示例:
```
type:get
url:http://192.168.0.220:8090/druid/indexer/v1/runningTasks
```
结果示例:
```JSON
[
    {
        "id": "batch_lucene_index_kafka_com_SJLnjowGe_project_BJerkXI8tb_b124069ea1a108f_bfekbhkn",
        "createdTime": "2017-09-08T01:36:31.175Z",
        "queueInsertionTime": "2017-09-08T01:36:31.225Z",
        "location": {
            "host": "dev222.sugo.net",
            "port": 8100
        }
    }
]
```

- `/druid/indexer/v1/completeTasks`  
**基本信息**   
接口说明:获取已完成任务的任务列表   
请求方式:GET  
请求地址:`/druid/indexer/v1/completeTasks`  
响应类型:application/json  
数据类型:\*\/\*   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |  
请求示例:
```
type:get
url:http://192.168.0.220:8090/druid/indexer/v1/completeTasks
```
结果示例:
```JSON
[
    {
        "id": "lucene_index_kafka_com_SJLnjowGe_project_B1edDad15Z_67f576f39c12b8d_mjfoadpp",
        "status": "SUCCESS",
        "duration": "189,622",
        "createdTime": "2017-09-08T02:18:38.150Z",
        "topic": "com_SJLnjowGe_project_B1edDad15Z",
        "offsets": "{\"0\":0}"
    }
]
```

- `/druid/indexer/v1/workers`  
**基本信息**   
接口说明:获取worker列表   
请求方式:GET  
请求地址:`/druid/indexer/v1/workers`  
响应类型:application/json  
数据类型:无   
请求参数:无  

请求示例:
```
type:get
url:http://192.168.0.220:8090/druid/indexer/v1/workers
```
结果示例:
```JSON
[
    {
        "worker": {
            "host": "dev221.sugo.net:8091",
            "ip": "dev221.sugo.net",
            "capacity": 3,
            "version": "0"
        },
        "currCapacityUsed": 3,
        "availabilityGroups": [
            "batch_lucene_index_kafka_com_SJLnjowGe_project_BySs3_IFb_11120a006c61898",
            "lucene_index_kafka_com_SJLnjowGe_project_Hk55wdy5b_a6f92473e2ff51c",
            "lucene_index_kafka_com_SJLnjowGe_project_H1kYxFycZ_5f3d03b0f13af37"
        ],
        "runningTasks": [
            "lucene_index_kafka_com_SJLnjowGe_project_Hk55wdy5b_a6f92473e2ff51c_jklfidgm",
            "lucene_index_kafka_com_SJLnjowGe_project_H1kYxFycZ_5f3d03b0f13af37_llmkcahm",
            "batch_lucene_index_kafka_com_SJLnjowGe_project_BySs3_IFb_11120a006c61898_ohdlncjd"
        ],
        "lastCompletedTaskTime": "2017-09-08T02:37:15.049Z"
    }
    ...
]
``` 
>注意: 结果示例中的`...`代表还有一些类似结构的结果出于减少篇幅的考虑没有列出来,后面例子中出现这个符号时也是一样的意思

- `/druid/indexer/v1/scaling`  
**基本信息**   
接口说明:获取扩展信息   
请求方式:GET  
请求地址:`/druid/indexer/v1/scaling`  
响应类型:application/json  
数据类型:无   
请求参数:无  

请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/scaling
```
结果示例:
```JSON
``` 

- `/druid/indexer/v1/task/{taskid}/log`   
**基本信息**  
接口说明: 获取指定task(task Id)运行日志信息  
请求方式:GET  
请求地址:`/druid/indexer/v1/task/{taskid}/log`  
响应类型:text/plain  
数据类型:\*/\*   
请求参数:   
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    taskid | 是 | string | 任务标识 | 
    offset | 否 | int | 日志偏移量(单位:字节) | 0
请求示例:
```
type:get
url:http://192.168.0.212:8090/druid/indexer/v1/task/batch_lucene_index_kafka_com_SJLnjowGe_project_BJerkXI8tb_b124069ea1a108f_bfekbhkn/log?offset=-8192

```
结果示例:
```
2017-09-08 10:45:37.854 INFO [MonitorScheduler-0] com.metamx.emitter.core.LoggingEmitter - Event [{"feed":"metrics","timestamp":"2017-09-08T02:45:37.854Z","service":"druid/middleManager","host":"dev222.sugo.net:8100","metric":"ingest/persists/count","value":0,"dataSource":"com_SJLnjowGe_project_BJerkXI8tb","taskId":["batch_lucene_index_kafka_com_SJLnjowGe_project_BJerkXI8tb_b124069ea1a108f_bfekbhkn"]}]
...
``` 

---
## 2. `CoordinatorResource`
> class : `io.druid.server.http.CoordinatorResource`

- `/druid/coordinator/v1/leader`  
**基本信息**   
接口说明:查看Coordinator的leader   
请求方式:GET  
请求地址:`/druid/coordinator/v1/leader`  
响应类型:application/json  
数据类型:无   
请求参数:无  

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/leader
```
结果示例:
```
192.168.0.212:8081
``` 
- `/druid/coordinator/v1/loadstatus`  
**基本信息**   
接口说明:查看数据加载情况  
请求方式:GET  
请求地址:`/druid/coordinator/v1/loadstatus`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    simple | 否 | string | 查看可用数据段 | 
    full | 否 | string | 查看各个数据源的详细情况(包括replicas) | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/loadstatus?simple=true
```
结果示例:
```json
{
    "wuxianji-metaq-1": 0,
    "com_HyoaKhQMl_project_rJxP5Y50DZ": 0,
    "wuxianji-metaq-2": 0,
    ...
}
``` 

- `/druid/coordinator/v1/loadqueue`  
**基本信息**   
接口说明:查看正在加载或者删除的数据段   
请求方式:GET  
请求地址:`/druid/coordinator/v1/loadqueue`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    simple | 否 | string | 查看待加载/待抛弃数据段的数量和大小 | 
    full | 否 | string | 查看所有的明细 | 

---

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/loadqueue?simple=true
```
结果示例:
```json
{
    "192.168.0.212:8083": {
        "segmentsToLoad": 0,
        "segmentsToDrop": 0,
        "segmentsToLoadSize": 0,
        "segmentsToDropSize": 0
    }
}
``` 

## 3. `HistoricalResource`
> class : `io.druid.server.http.HistoricalResource`

- `/druid/historical/v1/loadstatus`  
**基本信息**   
接口说明:查看节点加载状态   
请求方式:GET  
请求地址:`/druid/historical/v1/loadstatus`  
响应类型:application/json  
数据类型:无   
请求参数:无  

请求示例:
```
type:get
url:http://192.168.0.212:8083/druid/historical/v1/loadstatus
```
结果示例:
```json
{
    "cacheInitialized": true
}
```

---

## 4. `MetadataResource`
> class : `io.druid.server.http.MetadataResource`

- `/druid/coordinator/v1/metadata/datasources`  
**基本信息**   
接口说明:查看所有的数据源元信息   
请求方式:GET  
请求地址:`/druid/coordinator/v1/metadata/datasources`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    includeDisabled | 否 | string | 指定是否查看的数据源包括已经屏蔽的数据源 |
    full | 否 | string | 查看所有数据源的明细 | 
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |
请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/metadata/datasources/?includeDisabled=true
```
结果示例:
```json
[
    "wuxianji-metaq-1",
    "com_HyoaKhQMl_project_rJxP5Y50DZ",
    "wuxianji-metaq-2",
    "wuxianjiRollup",
    ...
]
```

- `/druid/coordinator/v1/metadata/datasources/{dataSourceName}`  
**基本信息**   
接口说明:查看指定数据源的元数据信息，包括数据段信息   
请求方式:GET  
请求地址:`/druid/coordinator/v1/metadata/datasources/{dataSourceName}`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 要查看的数据源名字 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/metadata/datasources/druid-test001
```
结果示例:
```json
{
    "name": "druid-test001",
    "properties": {
        "created": "2017-09-08T06:02:12.027Z"
    },
    "segments": [
        {
            "dataSource": "druid-test001",
            "interval": "2017-05-01T00:00:00.000Z/2017-05-02T00:00:00.000Z",
            "version": "2017-08-30T07:40:21.179Z",
            "loadSpec": {
                "type": "local",
                "path": "/data1/druid/storage/druid-test001/2017-05-01T00:00:00.000Z_2017-05-02T00:00:00.000Z/2017-08-30T07:40:21.179Z/0/index.zip"
            },
            "dimensions": "",
            "metrics": "",
            "shardSpec": {
                "type": "linear",
                "partitionNum": 0
            },
            "binaryVersion": 9,
            "size": 12238777,
            "identifier": "druid-test001_2017-05-01T00:00:00.000Z_2017-05-02T00:00:00.000Z_2017-08-30T07:40:21.179Z"
        },
        ...
    ]
}
```

- `/druid/coordinator/v1/metadata/datasources/{dataSourceName}/segments`  
**基本信息**   
接口说明:查看指定数据源的数据段信息
请求方式:GET  
请求地址:`/druid/coordinator/v1/metadata/datasources/{dataSourceName}/segments`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 要查看的数据源名字 | 
    full | 否 | string | 查看所有数据段的明细 | 
请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/metadata/datasources/druid-test001/segments
```
结果示例:
```json
[
    {
        "dataSource": "druid-test001",
        "interval": "2017-05-01T00:00:00.000Z/2017-05-02T00:00:00.000Z",
        "version": "2017-08-30T07:40:21.179Z",
        "loadSpec": {
            "type": "local",
            "path": "/data1/druid/storage/druid-test001/2017-05-01T00:00:00.000Z_2017-05-02T00:00:00.000Z/2017-08-30T07:40:21.179Z/0/index.zip"
        },
        "dimensions": "",
        "metrics": "",
        "shardSpec": {
            "type": "linear",
            "partitionNum": 0
        },
        "binaryVersion": 9,
        "size": 12238777,
        "identifier": "druid-test001_2017-05-01T00:00:00.000Z_2017-05-02T00:00:00.000Z_2017-08-30T07:40:21.179Z"
    },
    ...
]
```

- `/druid/coordinator/v1/metadata/datasources/{dataSourceName}/segments`  
**基本信息**   
接口说明:查看数据源指定时间范围内的数据段
请求方式:POST  
请求地址:`/druid/coordinator/v1/metadata/datasources/{dataSourceName}/segments`  
响应类型:application/json  
数据类型:application/json      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 要查看的数据源名字 | 
    full | 否 | string | 查看所有的明细 | 
    {Body数据} | 否 | json_string | 指定要查询的数据段时间范围 |

请求示例:
```
type:post
url:http://192.168.0.212:8081/druid/coordinator/v1/metadata/datasources/druid-test001/segments  
Body数据:
["2017-05-20T00:00:00.000Z/2017-05-21T23:59:59.999Z"]
```
结果示例:
```json
[
    "druid-test001_2017-05-20T00:00:00.000Z_2017-05-21T00:00:00.000Z_2017-08-30T07:40:21.179Z",
    "druid-test001_2017-05-21T00:00:00.000Z_2017-05-22T00:00:00.000Z_2017-08-30T07:40:21.179Z"
]
```

- `/druid/coordinator/v1/metadata/datasources/{dataSourceName}/segments/{segmentId}`  
**基本信息**   
接口说明:查看数据源中segmentId对应的数据段信息
请求方式:GET  
请求地址:`/druid/coordinator/v1/metadata/datasources/{dataSourceName}/segments/{segmentId}`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 要查看的数据源名字 | 
    segmentId | 否 | string | 要查看的数据段标识 | 
请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/metadata/datasources/druid-test001/segments/druid-test001_2017-05-20T00:00:00.000Z_2017-05-21T00:00:00.000Z_2017-08-30T07:40:21.179Z  
```
结果示例:
```json
{
    "dataSource": "druid-test001",
    "interval": "2017-05-20T00:00:00.000Z/2017-05-21T00:00:00.000Z",
    "version": "2017-08-30T07:40:21.179Z",
    "loadSpec": {
        "type": "local",
        "path": "/data1/druid/storage/druid-test001/2017-05-20T00:00:00.000Z_2017-05-21T00:00:00.000Z/2017-08-30T07:40:21.179Z/0/index.zip"
    },
    "dimensions": "",
    "metrics": "",
    "shardSpec": {
        "type": "linear",
        "partitionNum": 0
    },
    "binaryVersion": 9,
    "size": 12238084,
    "identifier": "druid-test001_2017-05-20T00:00:00.000Z_2017-05-21T00:00:00.000Z_2017-08-30T07:40:21.179Z"
}
```

---

## ５. `RulesResource`
> class : `io.druid.server.http.RulesResource`

- `/druid/coordinator/v1/rules`  
**基本信息**   
接口说明:查看所有目前有效的Rule
请求方式:GET  
请求地址:`/druid/coordinator/v1/rules`  
响应类型:application/json  
数据类型:\*/\*      
请求参数: 无

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/rules
```
结果示例:
```json
{
    "_default": [
        {
            "tieredReplicants": {
                "_default_tier": 1
            },
            "type": "loadForever"
        }
    ],
    "zty0801": [
        {
            "type": "dropForever"
        }
    ],
    "testData0616": [
        {
            "type": "dropForever"
        }
    ],
    ...
}
```


- `/druid/coordinator/v1/rules/{dataSourceName}`  
**基本信息**   
接口说明:查看指定数据源的Rule
请求方式:GET  
请求地址:`/druid/coordinator/v1/rules/{dataSourceName}`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 要查看的数据源名字 | 
    full | 否 | string | 是否查看默认规则 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/rules/druid-test001
```
结果示例:
```json
[]
```


- `/druid/coordinator/v1/rules/{dataSourceName}`  
**基本信息**   
接口说明:设置数据源的Rule   
请求方式:POST  
请求地址:`/druid/coordinator/v1/rules/{dataSourceName}`  
响应类型:application/json  
数据类型:application/json      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 要查看的数据源名字 | 
    {Body数据} | 是 | json_string | 要设置的规则,具体的json定义规则可参考[druid社区Rule](http://druid.io/docs/latest/operations/rule-configuration.html) |
    X-Druid-Author{HeaderParam} | 否 | string | 说明设置者是谁 | ""
    X-Druid-Comment{HeaderParam} | 否 | string | 对此次设置进行说明 | ""  
    
请求示例:
```
type:post
url:http://192.168.0.212:8081/druid/coordinator/v1/rules/userinfo1
Body数据:
[
	{
	  "type" : "loadByInterval",
	  "interval": "2016-01-01/2017-01-01"
	}
]
```
结果示例:
```json
```

- `/druid/coordinator/v1/rules/{dataSourceName}/history`  
**基本信息**   
接口说明:查看数据源Rule的修改历史记录
请求方式:GET  
请求地址:`/druid/coordinator/v1/rules/{dataSourceName}/history`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 要查看的数据源名字 | 
    interval | 否 | string | 设置查询的时间区间 | 
    count | 否 | int | 设置查询的总记录数 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/rules/userinfo1/history
```
结果示例:
```json
[
    {
        "key": "userinfo1",
        "type": "rules",
        "auditInfo": {
            "author": "",
            "comment": "",
            "ip": "192.168.0.25"
        },
        "payload": [{"interval":"2012-01-01T00:00:00.000Z/2018-01-01T00:00:00.000Z","tieredReplicants":{"_default_tier":2},"type":"loadByInterval"}],
        "auditTime": "2017-09-08T07:20:23.255Z"
    }
]
```

- `/druid/coordinator/v1/rules/history`  
**基本信息**   
接口说明:查看所有Rule的修改历史记录
请求方式:GET  
请求地址:`/druid/coordinator/v1/rules/history`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    interval | 否 | string | 设置查询的时间区间 | 
    count | 否 | int | 设置查询的总记录数 | 
请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/rules/history
```
结果示例:
```json
[
    {
        "key": "userinfo1",
        "type": "rules",
        "auditInfo": {
            "author": "",
            "comment": "",
            "ip": "192.168.0.25"
        },
        "payload": [{"tieredReplicants":{"_default_tier":2},"type":"loadForever"}],
        "auditTime": "2017-09-08T07:22:03.722Z"
    },
    ...
]
```

---

## 6. `DatasourcesResource`
> class : `io.druid.server.http.DatasourcesResource`

- `/druid/coordinator/v1/datasources`  
**基本信息**   
接口说明:查看所有的数据源
请求方式:GET  
请求地址:`/druid/coordinator/v1/datasources`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    simple | 否 | string | 查看所有数据源的统计情况 | 
    full | 否 | string | 查看所有数据源的明细 | 
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources
```
结果示例:
```json
[
    "com_HyoaKhQMl_project_B1l1zUfYOW",
    "com_HyoaKhQMl_project_BJxvpDuYuW",
    ...
]
```

- `/druid/coordinator/v1/datasources/{dataSourceName}`  
**基本信息**   
接口说明:查看指定数据源信息    
请求方式:GET  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}`  
响应类型:application/json  
数据类型:\*/\*      
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 要查看的数据源名字 | 
    full | 否 | string | 是否查看指定数据源的明细 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo
```
结果示例:
```json
{
    "tiers": {
        "_default_tier": {
            "size": 19187187,
            "segmentCount": 45
        }
    },
    "segments": {
        "maxTime": "2017-08-02T00:00:00.000Z",
        "size": 19187187,
        "minTime": "2017-07-30T00:00:00.000Z",
        "count": 45
    }
}
```

- `/druid/coordinator/v1/datasources/{dataSourceName}`  
**基本信息**   
接口说明:恢复数据源    
请求方式:POST  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}`  
响应类型:\*/\*  
数据类型:application/json       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 要恢复的数据源名字 | 

请求示例:
```
type:post
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/rollup-0901
```
结果示例:
```json
```

- `/druid/coordinator/v1/datasources/{dataSourceName}`  
**基本信息**   
接口说明:删除数据源   
请求方式:DELETE  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 要删除的数据源名字 | 
    kill | 否 | boolean | 指定删除的方式(启动Kill Task删除还是直接删除) | 
    interval | 否 | string | 指定要删除的数据段的时间区间 | 

请求示例:
```
type:delete
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/rollup-0901
```
结果示例:
```json
```

- `/druid/coordinator/v1/datasources/{dataSourceName}/intervals`  
**基本信息**   
接口说明:查看数据源所有时间段  
请求方式:GET  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}/intervals`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 指定数据源的名字 | 
    simple | 否 | string | 查看数据源各个时间段中数据段的大小和数量 | 
    full | 否 | string | 查看数据源各个时间段中数据段的详细情况 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo/intervals
```
结果示例:
```json
[
    "2017-08-01T00:00:00.000Z/2017-08-02T00:00:00.000Z",
    "2017-07-31T00:00:00.000Z/2017-08-01T00:00:00.000Z",
    "2017-07-30T00:00:00.000Z/2017-07-31T00:00:00.000Z"
]
```
    
- `/druid/coordinator/v1/datasources/{dataSourceName}/intervals/{interval}`  
**基本信息**   
接口说明:查看指定时间段的数据段    
请求方式:GET  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}/intervals/{interval}`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 指定数据源的名字 | 
    interval | 是 | string | 查看的时间段,interval的格式如：`2017-03-24T01:00:00.000Z_2017-03-24T02:00:00.000Z` | 
    simple | 否 | string | 查看指定时间段中数据段的大小和数量 | 
    full | 否 | string | 查看指定时间段中数据段的详细情况 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo/intervals/2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z
```
结果示例:
```json
[
    "userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z_1",
    "userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z",
    ...
]
```

- `/druid/coordinator/v1/datasources/{dataSourceName}/segments`  
**基本信息**   
接口说明:查看数据源所有的数据段      
请求方式:GET  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}/segments`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 指定数据源的名字 | 
    full | 否 | string | 查看数据段的详细情况 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo/segments
```
结果示例:
```json
[
    "userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z",
    "userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z_1",
    "userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z_10",
    ...
]
```

- `/druid/coordinator/v1/datasources/{dataSourceName}/segments/{segmentId}`  
**基本信息**   
接口说明:查看数据源指定数据段信息      
请求方式:GET  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}/segments/{segmentId}`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 指定数据源的名字 | 
    segmentId | 是 | string | 指定segment标识 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo/segments/userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z
```
结果示例:
```json
{
    "metadata": {
        "dataSource": "userinfo",
        "interval": "2017-07-30T00:00:00.000Z/2017-07-31T00:00:00.000Z",
        "version": "2017-08-01T04:58:26.782Z",
        "loadSpec": {
            "type": "local",
            "path": "/data1/druid/storage/userinfo/2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z/2017-08-01T04:58:26.782Z/0/index.zip"
        },
        "dimensions": "",
        "metrics": "",
        "shardSpec": {
            "type": "numbered",
            "partitionNum": 0,
            "partitions": 0
        },
        "binaryVersion": 9,
        "size": 411764,
        "identifier": "userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z"
    },
    "servers": [
        "192.168.0.212:8083"
    ]
}
```

- `/druid/coordinator/v1/datasources/{dataSourceName}/segments/{segmentId}`  
**基本信息**   
接口说明:删除数据源指定数据段      
请求方式:DELETE  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}/segments/{segmentId}`  
响应类型:\*/\*  
数据类型:\*/\*       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 指定数据源的名字 | 
    segmentId | 是 | string | 指定segment标识 |

请求示例:
```
type:delete
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo/segments/userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z
```
结果示例:
```json
```     

- `/druid/coordinator/v1/datasources/{dataSourceName}/segments/{segmentId}`  
**基本信息**   
接口说明:恢复数据源指定数据段      
请求方式:POST  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}/segments/{segmentId}`  
响应类型:\*/\*  
数据类型:application/json       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 指定数据源的名字 | 
    segmentId | 是 | string | 指定segment标识 | 

请求示例:
```
type:post
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo/segments/userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z
```
结果示例:
```json
```  

- `/druid/coordinator/v1/datasources/{dataSourceName}/tiers`  
**基本信息**   
接口说明:查看数据源分布的组      
请求方式:GET  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}/tiers`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 指定数据源的名字 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo/tiers
```
结果示例:
```json
[
    "_default_tier"
]
``` 

- `/druid/coordinator/v1/datasources/{dataSourceName}/intervals/{interval}/serverview`  
**基本信息**   
接口说明:查看时间段内各数据段在各节点的分布情况    
请求方式:GET  
请求地址:`/druid/coordinator/v1/datasources/{dataSourceName}/intervals/{interval}/serverview`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    dataSourceName | 是 | string | 指定数据源的名字 | 
    interval | 是 | string | 指定时间段 | 

请求示例:
```
type:get
url:http://192.168.0.212:8081/druid/coordinator/v1/datasources/userinfo/intervals/2017-08-01T00:00:00.000Z_2017-08-02T00:00:00.000Z/serverview
```
结果示例:
```json
[
    {
        "segment": {
            "dataSource": "userinfo",
            "interval": "2017-08-01T00:00:00.000Z/2017-08-02T00:00:00.000Z",
            "version": "2017-08-01T04:58:26.264Z",
            "loadSpec": {
                "type": "local",
                "path": "/data1/druid/storage/userinfo/2017-08-01T00:00:00.000Z_2017-08-02T00:00:00.000Z/2017-08-01T04:58:26.264Z/0/index.zip"
            },
            "dimensions": "",
            "metrics": "",
            "shardSpec": {
                "type": "numbered",
                "partitionNum": 0,
                "partitions": 0
            },
            "binaryVersion": 9,
            "size": 116235,
            "identifier": "userinfo_2017-08-01T00:00:00.000Z_2017-08-02T00:00:00.000Z_2017-08-01T04:58:26.264Z"
        },
        "servers": [
            {
                "name": "192.168.0.212:8083",
                "host": "192.168.0.212:8083",
                "maxSize": 130000000000,
                "type": "historical",
                "tier": "_default_tier",
                "priority": 0
            }
        ]
    },
]
``` 

---
## 7. `SupervisorResource`
> class : `io.druid.indexing.overlord.supervisor.SupervisorResource`

- `/druid/indexer/v1/supervisor`  
**基本信息**   
接口说明:创建supervisor服务      
请求方式:POST  
请求地址:`/druid/indexer/v1/supervisor`  
响应类型:application/json  
数据类型:application/json       
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    {Body数据} | 是 | json_string | 配置supervisor服务的json字符串 | 
>说明：  
1、如果相同数据源的supervisor已经存在，则会导致正在运行的supervisor会通知其管理的所有任务终止读取并执行segment发布。  
2、退正在执行的supervisor。  
3、使用Request请求体内的规范创建一个新的supervisor，它会接管处于发布状态的任务，已经创建新的任务从处于发布状态的任务的介绍offset处开始读取数据。

- `/druid/indexer/v1/supervisor`  
**基本信息**   
接口说明:获取当前活跃的supervisor列表      
请求方式:GET  
请求地址:`/druid/indexer/v1/supervisor`  
响应类型:application/json  
数据类型:无       
请求参数:无 

请求示例:
```
type:get
url:http://192.168.0.220:8090/druid/indexer/v1/supervisor
```
结果示例:
```json
[
    "com_SJLnjowGe_project_r1MqbHhy5b",
    "com_SJLnjowGe_project_HygkE83y9b",
    "com_SJLnjowGe_project_ryEljIIKb",
    "com_SJLnjowGe_project_BJerkXI8tb",
    "com_SJLnjowGe_project_H1ejJETYZ",
    "com_SJLnjowGe_project_BySs3_IFb",
    "com_SJLnjowGe_project_Hyl3t5rptZ"
]
``` 

- `/druid/indexer/v1/supervisor/{id}`  
**基本信息**   
接口说明:获取指定的supervisor信息          
请求方式:GET  
请求地址:`/druid/indexer/v1/supervisor/{id}`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定supervisor标识 |  

请求示例:
```
type:get
url:http://192.168.0.220:8090/druid/indexer/v1/supervisor/com_SJLnjowGe_project_r1MqbHhy5b
```
结果示例:
```json
{
    "type": "lucene_supervisor",
    "dataSchema": {"some json spec"},
    "tuningConfig": {"some json spec"},
    ...
}
```

- `/druid/indexer/v1/supervisor/{id}/status`  
**基本信息**   
接口说明:获取指定supervisorId的状态          
请求方式:GET  
请求地址:`/druid/indexer/v1/supervisor/{id}/status`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定supervisor标识 |

请求示例:
```
type:get
url:http://192.168.0.220:8090/druid/indexer/v1/supervisor/com_SJLnjowGe_project_r1MqbHhy5b/status
```
结果示例:
```json
{
    "id": "com_SJLnjowGe_project_r1MqbHhy5b",
    "generationTime": "2017-09-08T08:38:48.985Z",
    "payload": {
        "dataSource": "com_SJLnjowGe_project_r1MqbHhy5b",
        "topic": "com_SJLnjowGe_project_r1MqbHhy5b",
        "partitions": 1,
        "replicas": 1,
        "durationSeconds": 7200,
        "activeTasks": [
            {
                "id": "lucene_index_kafka_com_SJLnjowGe_project_r1MqbHhy5b_9edbdd5daf0e5d2_pdnnpmai",
                "startingOffsets": {
                    "0": 0
                },
                "startTime": "2017-09-08T06:42:16.559Z",
                "remainingSeconds": 207,
                "type": "ACTIVE",
                "currentOffsets": {
                    "0": 16
                }
            }
        ],
        "publishingTasks": []
    }
}
```  

- `/druid/indexer/v1/supervisor/{id}/shutdown`  
**基本信息**   
接口说明:关闭supervisorId指定的supervisor，同时使其管理的所有任务终止读取，进入segment发布状态          
请求方式:POST  
请求地址:`/druid/indexer/v1/supervisor/{id}/shutdown`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定supervisor标识 | 

- `/druid/indexer/v1/supervisor/history`  
**基本信息**   
接口说明:查看所有supervisor的历史          
请求方式:GET  
请求地址:`/druid/indexer/v1/supervisor/history`  
响应类型:application/json  
数据类型:无   
请求参数:无

请求示例:
```
type:get
url:http://192.168.0.220:8090/druid/indexer/v1/supervisor/history
```
结果示例:
```json
{
    "com_SJLnjowGe_project_Hkm8wSTuW": [
        {
            "spec": {
                "type": "NoopSupervisorSpec"
            },
            "version": "2017-08-25T07:31:53.929Z"
        },
        {"some json spec"},
    ]
    ...
}
``` 

- `/druid/indexer/v1/supervisor/{id}/history`  
**基本信息**   
接口说明:获取指定supervisor(supervisor Id)的历史            
请求方式:GET  
请求地址:`/druid/indexer/v1/supervisor/{id}/history`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定supervisor标识 | 


- `/druid/indexer/v1/supervisor/{id}/reset`  
**基本信息**   
接口说明:重置消费位置            
请求方式:POST  
请求地址:`/druid/indexer/v1/supervisor/{id}/reset`  
响应类型:application/json  
数据类型:\*/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定supervisor标识 | 

请求示例:
```
type:get
url:http://192.168.0.220:8090/druid/indexer/v1/supervisor/com_SJLnjowGe_project_BJerkXI8tb/reset
```
结果示例:
```json
{
    "id": "com_SJLnjowGe_project_BJerkXI8tb"
}
```

- `/druid/indexer/v1/supervisor/topic/{id}/delete`  
**基本信息**   
接口说明:删除指定的kafka topic            
请求方式:POST  
请求地址:`/druid/indexer/v1/supervisor/topic/{id}/delete`  
响应类型:text/plain  
数据类型:\*/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定topic标识 | 

---

## 8. `ChatHandlerResource`
> class : `io.druid.segment.realtime.firehose.ChatHandlerResource`

- `/druid/worker/v1/chat/{id}`  
**基本信息**   
接口说明:查看指定的indexTask信息            
请求方式:GET  
请求地址:`/druid/worker/v1/chat/{id}`  
响应类型:\*/\*  
数据类型:\*/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定indexTask标识 | 
    X-Druid-Task-Id{HeaderParam} | 否 | string |  | ""

---

## 9. `CoordinatorDynamicConfigsResource`
> class : `io.druid.server.http.CoordinatorDynamicConfigsResource`

- `/druid/coordinator/v1/config`  
**基本信息**   
接口说明:获取coordinator的配置信息            
请求方式:GET  
请求地址:`/druid/coordinator/v1/config`  
响应类型:application/json   
数据类型:无     
请求参数:无

请求示例:
```
type:get
url:http://192.168.0.212.:8081/druid/coordinator/v1/config
```
结果示例:
```json
{
    "millisToWaitBeforeDeleting": 900000,
    "mergeBytesLimit": 524288000,
    "mergeSegmentsLimit": 100,
    "maxSegmentsToMove": 0,
    "replicantLifetime": 15,
    "replicationThrottleLimit": 10,
    "balancerComputeThreads": 1,
    "emitBalancingStats": false,
    "killDataSourceWhitelist": null
}
```

- `/druid/coordinator/v1/config`  
**基本信息**   
接口说明:设置coordinator的配置信息            
请求方式:POST  
请求地址:`/druid/coordinator/v1/config`  
响应类型:\*/\*   
数据类型:application/json     
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    {Body数据} | 是 | json_string |配置信息的json字符串 |
    X-Druid-Author{HeaderParam} | 否 | string | 说明设置者是谁 | ""
    X-Druid-Comment{HeaderParam} | 否 | string | 对此次设置进行说明 | ""

请求示例:
```
type:post
url:http://192.168.0.212.:8081/druid/coordinator/v1/config
Body数据:
{
    "millisToWaitBeforeDeleting": 100000,
    "mergeBytesLimit": 524288000,
    "mergeSegmentsLimit": 100,
    "maxSegmentsToMove": 0,
    "replicantLifetime": 15,
    "replicationThrottleLimit": 10,
    "balancerComputeThreads": 1,
    "emitBalancingStats": false,
}
```
结果示例:
```json
```

- `/druid/coordinator/v1/config/history`  
**基本信息**   
接口说明:查看历史的coordinator配置信息            
请求方式:GET  
请求地址:`/druid/coordinator/v1/config/history`  
响应类型:application/json   
数据类型:\*/\*     
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    interval | 否 | string | 查询的历史区间 | 
    count | 否 | int | 要获取的结果数 | 

请求示例:
```
type:get
url:http://192.168.0.212.:8081/druid/coordinator/v1/config/history
```
结果示例:
```json
[
    {
        "key": "coordinator.config",
        "type": "coordinator.config",
        "auditInfo": {
            "author": "",
            "comment": "",
            "ip": "192.168.0.25"
        },
        "payload": {"millisToWaitBeforeDeleting":900000,"mergeBytesLimit":524288000,"mergeSegmentsLimit":100,"maxSegmentsToMove":0,"replicantLifetime":15,"replicationThrottleLimit":10,"balancerComputeThreads":1,"emitBalancingStats":false,"killDataSourceWhitelist":null},
        "auditTime": "2017-09-08T09:20:50.923Z"
    }
]
```

---

## 10. `IntervalsResource`
> class : `io.druid.server.http.IntervalsResource`

- `/druid/coordinator/v1/intervals`  
**基本信息**   
接口说明:查看所有的时间段信息            
请求方式:GET  
请求地址:`/druid/coordinator/v1/intervals`  
响应类型:application/json   
数据类型:\*\/\*     
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |

请求示例:
```
type:get
url:http://192.168.0.212.:8081/druid/coordinator/v1/intervals
```
结果示例:
```json
{
    "2017-12-31T00:00:00.000Z/2018-01-01T00:00:00.000Z": {
        "com_HyoaKhQMl_project_H1qebDt_b": {
            "size": 170648,
            "count": 2
        },
        "com_HyoaKhQMl_project_SkNWMBwtdb": {
            "size": 85333,
            "count": 1
        },
        "com_HyoaKhQMl_project_ryOVFvYOW": {
            "size": 85324,
            "count": 1
        },
        "com_HyoaKhQMl_project_HJg582IK_Z": {
            "size": 84261,
            "count": 1
        },
        "com_HyoaKhQMl_project_S1lb4lDKuZ": {
            "size": 84261,
            "count": 1
        },
        "com_HyoaKhQMl_project_rJxcL7ItOW": {
            "size": 84261,
            "count": 1
        }
    },
    ...
}
```

- `/druid/coordinator/v1/intervals/{interval}`  
**基本信息**   
接口说明:查看指定时间段的信息            
请求方式:GET  
请求地址:`/druid/coordinator/v1/intervals/{interval}`  
响应类型:application/json   
数据类型:\*/\*     
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    interval | 是 | string | 指定要查询的时间段 | 
    simple | 否 | string | 查看时间段中包含的数据段的大小和数量 | 
    full | 否 | string | 查看时间段的详细情况 | 
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |

请求示例:
```
type:get
url:http://192.168.0.212.:8081/druid/coordinator/v1/intervals/2017-12-31T00:00:00.000Z_2018-01-01T00:00:00.000Z
```
结果示例:
```json
{
    "size": 594088,
    "count": 7
}
```

---

## 11. `LookupListeningResource`
> class : `io.druid.query.lookup.LookupModule$LookupListeningResource`

- `/druid/listen/v1/lookups`  
**基本信息**   
接口说明:更新多个loopup            
请求方式:POST  
请求地址:`/druid/listen/v1/lookups`  
响应类型:application/json,application/x-jackson-smile   
数据类型:application/json,application/x-jackson-smile      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    {Body数据} | 是 | json_string | 多个lookup的更新信息 |
    Content-Type{HeaderParam} | 是 | `application/json` \| `application/x-jackson-smile` | 说明Body数据的具体类型 | application/json

- `/druid/listen/v1/lookups`  
**基本信息**   
接口说明:查看本地(historical)所有loopup            
请求方式:GET  
请求地址:`/druid/listen/v1/lookups`  
响应类型:application/json,application/x-jackson-smile   
数据类型:无      
请求参数:无

请求示例:
```
type:get
url:http://192.168.0.222.:8083/druid/listen/v1/lookups
```
结果示例:
```json
{
     "usergroup_SyeZb6CRwb": {
        "version": "HyZWZpRCPZ",
        "dataLoader": {
            "type": "redis",
            "hostAndPorts": "192.168.0.220:6379",
            "clusterMode": false,
            "password": null,
            "groupId": "usergroup_SyeZb6CRwb",
            "updateTime": "2017-09-08T09:37:45.242Z"
        }
    },
    ...
}
```

- `/druid/listen/v1/lookups/{id}`  
**基本信息**   
接口说明:查看指定loopup的信息            
请求方式:GET  
请求地址:`/druid/listen/v1/lookups/{id}`  
响应类型:application/json,application/x-jackson-smile   
数据类型:\*\/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定lookup标识 |

请求示例:
```
type:get
url:http://192.168.0.222:8083/druid/listen/v1/lookups/usergroup_SyeZb6CRwb
```
结果示例:
```json
{
    "type": "cachingLookup",
    "version": "HyZWZpRCPZ",
    "dataLoader": {
        "type": "redis",
        "hostAndPorts": "192.168.0.220:6379",
        "clusterMode": false,
        "password": null,
        "groupId": "usergroup_SyeZb6CRwb",
        "updateTime": "2017-09-08T09:37:45.242Z"
    }
}
```

- `/druid/listen/v1/lookups/{id}`  
**基本信息**   
接口说明:删除本地的loopup(不是真是删除,redis里还存在数据)        
请求方式:DELETE  
请求地址:`/druid/listen/v1/lookups/{id}`  
响应类型:application/json,application/x-jackson-smile   
数据类型:\*\/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定lookup标识 |

请求示例:
```
type:delete
url:http://192.168.0.222.:8083/druid/listen/v1/lookups/8
```
结果示例:
```json
8
```

- `/druid/listen/v1/lookups/{id}`  
**基本信息**   
接口说明:更新指定loopup        
请求方式:POST  
请求地址:`/druid/listen/v1/lookups/{id}`  
响应类型:application/json,application/x-jackson-smile   
数据类型:\*\/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定lookup标识 |
    {Body数据} | 是 | json_string | lookup的更新信息 |
    Content-Type{HeaderParam} | 是 | `application/json` \| `application/x-jackson-smile` | 说明Body数据的具体类型 | application/json

请求示例:
```
type:post
url:http://192.168.0.222.:8083/druid/listen/v1/lookups/9
Body数据:
{
    "type": "cachingLookup",
    "version": "1",
    "dataFetcher": {
        "type": "redis",
        "hostAndPorts": "192.168.0.223:6379",
        "clusterMode": false,
        "password": null,
        "groupId": "9"
    }
}
```
结果示例:
```json
{
    "status": "accepted",
    "failedUpdates": {}
}
```

----

## 12. `LookupCoordinatorResource`
> class : `io.druid.server.http.LookupCoordinatorResource`

- `/druid/coordinator/v1/lookups`  
**基本信息**   
接口说明:查看所有的lookup组(tiers)            
请求方式:GET  
请求地址:`/druid/coordinator/v1/lookups`  
响应类型:application/json,application/x-jackson-smile   
数据类型:\*/\*     
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    discover | 否 | boolean | 是否有嵌套的lookup | false

请求示例:
```
type:get
url:http://192.168.0.222:8081/druid/coordinator/v1/lookups/
```
结果示例:
```json
[
    "__default"
]
```

- `/druid/coordinator/v1/lookups`  
**基本信息**   
接口说明:更新lookup组            
请求方式:POST  
请求地址:`/druid/coordinator/v1/lookups`  
响应类型:application/json,application/x-jackson-smile   
数据类型:application/json,application/x-jackson-smile     
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    {Body数据} | 是 | json_string | lookup组的更新信息 |
    X-Druid-Author{HeaderParam} | 否 | string | 说明设置者是谁 | ""
    X-Druid-Comment{HeaderParam} | 否 | string | 对此次设置进行说明 | ""
    Content-Type{HeaderParam} | 是 | `application/json` \| `application/x-jackson-smile` | 说明Body数据的具体类型 | application/json

请求示例:
```
type:post
url:http://192.168.0.222:8081/druid/coordinator/v1/lookups/
Body数据:  //初次创建lookup时需要发送一个空的json语句进行初始化
{}
```
结果示例:
```json
{}
```
    
- `/druid/coordinator/v1/lookups/{tier}/{lookup}`  
**基本信息**   
接口说明:删除lookup            
请求方式:DELETE  
请求地址:`/druid/coordinator/v1/lookups/{tier}/{lookup}`  
响应类型:application/json,application/x-jackson-smile   
数据类型:\*/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    tier | 是 | string | lookup所属的组 |
    lookup | 是 | string | 指定lookup标识 |
    X-Druid-Author{HeaderParam} | 否 | string | 说明设置者是谁 | ""
    X-Druid-Comment{HeaderParam} | 否 | string | 对此次设置进行说明 | ""

请求示例:
```
type:delete
url:http://192.168.0.222:8081/druid/coordinator/v1/lookups/__default/8
```
结果示例:
```json
```

- `/druid/coordinator/v1/lookups/{tier}/{lookup}`  
**基本信息**   
接口说明:更新lookup            
请求方式:POST  
请求地址:`/druid/coordinator/v1/lookups/{tier}/{lookup}`  
响应类型:application/json,application/x-jackson-smile   
数据类型:\*/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    {Body数据} | 是 | json_string | lookup的更新信息 |
    tier | 是 | string | lookup所属的组 |
    lookup | 是 | string | 指定lookup标识 |
    X-Druid-Author{HeaderParam} | 否 | string | 说明设置者是谁 | ""
    X-Druid-Comment{HeaderParam} | 否 | string | 对此次设置进行说明 | ""
    Content-Type{HeaderParam} | 是 | `application/json` \| `application/x-jackson-smile` | 说明Body数据的具体类型 | application/json

请求示例:
```
type:post
url:http://192.168.0.222:8081/druid/coordinator/v1/lookups/__default/8
Body数据:  //初次创建lookup时需要发送一个空的json语句进行初始化
{
    "type": "cachingLookup",
    "version": "1",
    "dataFetcher": {
        "type": "redis",
        "hostAndPorts": "192.168.0.223:6379",
        "clusterMode": false,
        "password": null,
        "groupId": "8"
    }
}
```
结果示例:
```json
```

- `/druid/coordinator/v1/lookups/{tier}/{lookup}`  
**基本信息**   
接口说明:查看指定lookup的说明信息            
请求方式:GET  
请求地址:`/druid/coordinator/v1/lookups/{tier}/{lookup}`  
响应类型:application/json,application/x-jackson-smile   
数据类型:\*/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    tier | 是 | string | lookup所属的组 |
    lookup | 是 | string | 指定lookup标识 |

请求示例:
```
type:get
url:http://192.168.0.222:8081/druid/coordinator/v1/lookups/__default/9
```
结果示例:
```json
{
    "type": "cachingLookup",
    "version": "1",
    "dataFetcher": {
        "type": "redis",
        "hostAndPorts": "192.168.0.223:6379",
        "clusterMode": false,
        "password": null,
        "groupId": "9"
    }
}
```

- `/druid/coordinator/v1/lookups/{tier}`  
**基本信息**   
接口说明:查看tier下的所有lookup            
请求方式:GET  
请求地址:`/druid/coordinator/v1/lookups/{tier}`  
响应类型:application/json,application/x-jackson-smile   
数据类型:\*/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    tier | 是 | string | lookup所属的组 |

请求示例:
```
type:get
url:http://192.168.0.222:8081/druid/coordinator/v1/lookups/__default/
```
结果示例:
```json
[
    "usergroup_SygU8Vly_b",
    "usergroup_rkx3CXkJ_b",
    "usergroup_H1TkR6ADb",
    ...
]
```

---

## 13. `LookupIntrospectionResource`
> class : `io.druid.query.lookup.LookupIntrospectionResource`

- `/druid/v1/lookups/introspect/{lookupId}`  
**基本信息**   
接口说明:对lookup进行内省            
请求方式:GET  
请求地址:`/druid/v1/lookups/introspect/{lookupId}`  
响应类型:\*/\*   
数据类型:\*/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    lookupId | 是 | string | 指定lookup标识 |

---

## 14. `QueryResource`
> class : `io.druid.server.QueryResource`

- `/druid/v2/queryCount`  
**基本信息**   
接口说明:查看Query成功,失败,中断的统计情况            
请求方式:GET  
请求地址:`/druid/v2/queryCount`  
响应类型:application/json   
数据类型:无      
请求参数:无

请求示例:
```
type:get
url:http://192.168.0.212:8082/druid/v2/queryCount
```
结果示例:
```json
{
    "success": 0,
    "failed": 0,
    "interrupted": 0
}
```

- `/druid/v2/queue`  
**基本信息**   
接口说明:查看Query队列            
请求方式:GET  
请求地址:`/druid/v2/queue`  
响应类型:application/json   
数据类型:无      
请求参数:无

请求示例:
```
type:get
url:http://192.168.0.212:8082/druid/v2/queue
```
结果示例:
```json
[]
```

- `/druid/v2/queue/{id}`  
**基本信息**   
接口说明:查看指定Query信息            
请求方式:GET  
请求地址:`/druid/v2/queue/{id}`  
响应类型:application/json   
数据类型:\*\/\*      
请求参数:    
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定query标识 |

- `/druid/v2/{id}`  
**基本信息**   
接口说明:删除Query            
请求方式:DELETE  
请求地址:`/druid/v2/{id}`  
响应类型:application/json   
数据类型:\*\/\*      
请求参数:    
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
    id | 是 | string | 指定query标识 |
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |

- `/druid/v2`  
**基本信息**   
接口说明:发送Query的json语句进行查询            
请求方式:POST  
请求地址:`/druid/v2`  
响应类型:application/json,application/x-jackson-smile    
数据类型:application/json,application/x-jackson-smile       
请求参数:    
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
     {Body数据} | 是 | json_string | 代表查询的json语句 |
     pretty | 否 | string | 指定返回的内容是否采用排版输出 |
    Druid-Auth-Token{RequestAttribute} | 否 | string | 认证口令 |
    Content-Type{HeaderParam} | 是 | `application/json` \| `application/x-jackson-smile` | 说明Body数据的具体类型 | 

请求示例:
```
type:post
url:http://192.168.0.212:8082/druid/v2/
Body数据:
{
    "queryType": "lucene_timeseries",
    "dataSource": "userinfo",
    "intervals": "1000/3000",
    "granularity": "all",
    "context": {
        "timeout": 180000,
        "useOffheap": true,
        "groupByStrategy": "v2"
    },
    "aggregations": [
        {
            "name": "__VALUE__",
            "type": "lucene_count"
        }
    ]
}
```
结果示例:
```json
[
    {
        "timestamp": "2017-07-30T00:00:00.000Z",
        "result": {
            "__VALUE__": 100000
        }
    }
]
```

---

## 15. `StatusResource`
> class : `io.druid.server.StatusResource`

- `/status`  
**基本信息**   
接口说明:查看系统已加载的模块           
请求方式:GET  
请求地址:`/status`  
响应类型:application/json    
数据类型:无       
请求参数:无 

请求示例:
```
type:get
url:http://192.168.0.212:8090/status
```
结果示例:
```json
{
    "version": "1.0.0",
    "memory": {
        "maxMemory": 1029177344,
        "totalMemory": 1029177344,
        "freeMemory": 951041872,
        "usedMemory": 78135472
    },
    "modules": [
        {
            "name": "io.druid.emitter.ingest.IngestEmitterModule",
            "artifact": "druid-lucene-extensions",
            "version": "1.0.0"
        },
        ...
    ]
}
    
```

---

## 16. `WorkerResource`
> class : `io.druid.indexing.worker.http.WorkerResource`

- `/druid/worker/v1/disable`  
**基本信息**   
接口说明:屏蔽worker           
请求方式:POST  
请求地址:`/druid/worker/v1/disable`  
响应类型:application/json    
数据类型:无       
请求参数:无 

请求示例:
```
type:get
url:http://192.168.0.212:8091/druid/worker/v1/disable
```
结果示例:
```json
{
    "192.168.0.212:8091": "disabled"
}
```

- `/druid/worker/v1/enable`  
**基本信息**   
接口说明:恢复worker           
请求方式:POST  
请求地址:`/druid/worker/v1/enable`  
响应类型:application/json    
数据类型:无       
请求参数:无 

请求示例:
```
type:get
url:http://192.168.0.212:8091/druid/worker/v1/enable
```
结果示例:
```json
{
    "192.168.0.212:8091": "enable"
}
```

- `/druid/worker/v1/enabled`  
**基本信息**   
接口说明:查看worker的使能状态           
请求方式:GET  
请求地址:`/druid/worker/v1/enabled`  
响应类型:application/json    
数据类型:无       
请求参数:无 

请求示例:
```
type:get
url:http://192.168.0.212:8091/druid/worker/v1/enabled
```
结果示例:
```json
{
    "192.168.0.212:8091": true
}
```

- `/druid/worker/v1/tasks`  
**基本信息**   
接口说明:查看正在运行的task           
请求方式:GET  
请求地址:`/druid/worker/v1/tasks`  
响应类型:application/json    
数据类型:无       
请求参数:无 

请求示例:
```
type:get
url:http://192.168.0.224:8091/druid/worker/v1/tasks
```
结果示例:
```json
[
    "lucene_index_kafka_wuxianjiRT_32ad42a1e7ee441_fimibgpk"
]
```

- `/druid/worker/v1/task/{taskid}/shutdown`  
**基本信息**   
接口说明:指定停止的Task           
请求方式:POST  
请求地址:`/druid/worker/v1/task/{taskid}/shutdown`  
响应类型:application/json    
数据类型:\*\/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
     taskid | 是 | string | 指定task的标识 | 

请求示例:
```
type:post
url:http://192.168.0.222:8091/druid/worker/v1/task/lucene_index_kafka_com_SJLnjowGe_project_H1ejJETYZ_5c415123134031b_kjecdaic/shutdown
```
结果示例:
```json
[
    "lucene_index_kafka_wuxianjiRT_32ad42a1e7ee441_fimibgpk"
]
```

- `/druid/worker/v1/task/{taskid}/log`  
**基本信息**   
接口说明:查看指定的Task日志           
请求方式:GET  
请求地址:`/druid/worker/v1/task/{taskid}/log`  
响应类型:text/plain    
数据类型:\*\/\*       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
     taskid | 是 | string | 指定task的标识 | 
     offset | 否 | long | 指定日志的偏移量 | 0

请求示例:
```
type:get
url:http://192.168.0.222:8091/druid/worker/v1/task/lucene_index_kafka_com_SJLnjowGe_project_H1ejJETYZ_5c415123134031b_mgndlbef/log
```
结果示例:
```
2017-09-08 19:57:42.707 INFO [main] io.druid.initialization.Initialization - added URL[file:/opt/apps/druidio_sugo/extensions/druid-lucene-extensions/lucene-sandbox-5.3.1.jar]
...
```
---

## 17. `BrokerResource`

>class : io.druid.server.http.BrokerResource

- `/druid/broker/v1/loadstatus`     
**基本信息**  
接口说明: 查看数据加载情况   
请求方式: GET  
请求地址: `/druid/broker/v1/loadstatus`  
响应类型: application/json  
数据类型: 无  
请求参数: 无  

请求示例：  
```
type: get 
url: http://192.168.0.212:8082/druid/broker/v1/loadstatus  
```
结果示例:
```json
{
    "inventoryInitialized": true
}
```
---

## 18. `BrokerQueryResource`  

>class : io.druid.server.BrokerQueryResource

- `/druid/v2/candidates`  
**基本信息**   
接口说明: 获取查询的数据库的段描述  
请求方式: POST  
请求地址: `/druid/v2/candidates`  
响应类型: application/json  
数据类型: application/json  
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    pretty | 否 | string | 如果不为空则以缩进的格式返回json字符串 |
    numCandidates | 否 | int | 指定server的数量（按照等级获取）,如果不指定，则server的数量为1 | -1
    Content-type{HeaderParam} | 是 | application/json | 说明body数据的类型 | 
    {Body数据} | 是 | json_string | json格式的查询 | 

请求示例：
```
type: post  
url: http://192.168.0.212:8082/druid/v2/candidates?numCandidates=1
Body数据:  
    {
    "queryType": "lucene_groupBy",
    "dataSource": "userinfo",
    "intervals": "1000/3000",
    "granularity": "all",
    "context": {
        "timeout": 180000,
        "useOffheap": true,
        "groupByStrategy": "v2"
    },
    "dimensions": [
        {
            "type": "default",
            "dimension": "province",
            "outputName": "province"
        }
    ],
    "aggregations": [
        {
            "name": "sum(age)",
            "type": "lucene_doubleSum",
            "fieldName": "age"
        }
    ],
    "limitSpec": {
        "type": "default",
        "columns": [
            {
                "dimension": "province"
            }
        ],
        "limit": 3
    }
}
```
结果示例:
```json
[
    {
        "interval": "2017-07-30T00:00:00.000Z/2017-07-31T00:00:00.000Z",
        "version": "2017-08-01T04:58:26.782Z",
        "partitionNumber": 0,
        "size": 411764,
        "locations": [
            {
                "name": "192.168.0.212:8083",
                "host": "192.168.0.212:8083",
                "maxSize": 130000000000,
                "type": "historical",
                "tier": "_default_tier",
                "priority": 0
            }
        ]
    },
    {
        "interval": "2017-07-30T00:00:00.000Z/2017-07-31T00:00:00.000Z",
        "version": "2017-08-01T04:58:26.782Z",
        "partitionNumber": 1,
        "size": 403238,
        "locations": [
            {
                "name": "192.168.0.212:8083",
                "host": "192.168.0.212:8083",
                "maxSize": 130000000000,
                "type": "historical",
                "tier": "_default_tier",
                "priority": 0
            }
        ]
    },
    ...
]
```

---

## 19. `ClientInfoResource`

>class : io.druid.server.ClientInfoResource

- `/druid/v2/datasources`  
**基本信息**   
接口说明: 获取所有数据源的名称  
请求方式: GET  
请求地址: `/druid/v2/datasources`  
响应类型: application/json  
数据类型: 无   
请求参数: 无

请求示例:  
```
type: get
url: http://192.168.0.212:8082/druid/v2/datasources  
```
结果示例:
```json
[
    "wuxianji-metaq-1",
    "wuxianji-metaq-2",
    "com_HyoaKhQMl_project_rJxP5Y50DZ",
]
```

- `/druid/v2/datasources/{dataSourceName}`  
**基本信息**   
接口说明: 获取指定数据库和区间的维度值和度量指标 
请求方式: GET  
请求地址: `/druid/v2/datasources/{dataSourceName}`  
响应类型: application/json  
数据类型: */*  
请求参数:  
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |                                                                      
    dataSourceName | 是 | string | 数据源的名称 |
    interval | 否 | string | 区间 | 一周前至当前时间 | 
    full | 否 | string | 是否查看所有信息 | null

请求示例:  
```
type: get 
url: http://192.168.0.212:8082/druid/v2/datasources/userinfo1?interval=1000/3000&full=true   
```
结果示例:
```json
```


- `/druid/v2/datasources/{dataSourceName}/dimensions`  
**基本信息**   
接口说明: 获取指定数据库和区间的维度值  
请求方式: GET  
请求地址: `/druid/v2/datasources/{dataSourceName}/dimensions`  
响应类型: application/json  
数据类型: */*   
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 数据源的名称 |
    interval | 否 | string | 区间 | 区间 | 一周前至当前时间

请求示例:  
```
type: get  
url: `http://192.168.0.212:8082/druid/v2/datasources/userinfo1/dimensions?interval=1000/3000`  
```
结果示例:
```json
```

- `/druid/v2/datasources/{dataSourceName}/metrics`  
**基本信息**   
接口说明: 获取指定数据库和区间的度量指标  
请求方式: GET  
请求地址: `/druid/v2/datasources/{dataSourceName}/metrics`  
响应类型: application/json  
数据类型: 无  
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 数据源的名称 |  
    interval | 否 | string | 区间 | 一周前至当前时间 |  

- `/druid/v2/datasources/{dataSourceName}/candidates`  
**基本信息**   
接口说明: 获取指定数据库和区间的段描述  
请求方式: GET  
请求地址: `/druid/v2/datasources/{dataSourceName}/candidates`   
响应类型: application/json  
数据类型: 无   
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值 
    ---- | ----- | --- | --- | ---- |   
    dataSourceName | 是 | string | 数据源的名称 |   
    interval | 否 | string | 区间 | 1000/3000
    numCandidates | 否 | int | 指定server的数量（按照等级获取）,如果不指定，则server的数量为1 | -1 

请求示例:  
```
type: get
url: http://192.168.0.212:8082/druid/v2/datasources/userinfo/candidates
```
结果示例:
```json
[
    {
        "interval": "2017-07-30T00:00:00.000Z/2017-07-31T00:00:00.000Z",
        "version": "2017-08-01T04:58:26.782Z",
        "partitionNumber": 0,
        "size": 411764,
        "locations": [
            {
                "name": "192.168.0.212:8083",
                "host": "192.168.0.212:8083",
                "maxSize": 130000000000,
                "type": "historical",
                "tier": "_default_tier",
                "priority": 0
            }
        ]
    },
    {
        "interval": "2017-07-30T00:00:00.000Z/2017-07-31T00:00:00.000Z",
        "version": "2017-08-01T04:58:26.782Z",
        "partitionNumber": 1,
        "size": 403238,
        "locations": [
            {
                "name": "192.168.0.212:8083",
                "host": "192.168.0.212:8083",
                "maxSize": 130000000000,
                "type": "historical",
                "tier": "_default_tier",
                "priority": 0
            }
        ]
    },
    ...
]
```


---

## 20. `ServersResource`

>class : io.druid.server.http.ServersResource

- `/druid/coordinator/v1/servers`  
**基本信息**   
接口说明: 获取所有的server的信息  
请求方式: GET  
请求地址: `/druid/coordinator/v1/servers`  
响应类型: application/json  
数据类型: 无   
请求参数: 
    参数名 | 是否必须 | 类型 | 描述  | 默认值 
    ---- | ----- | --- | --- | ---- |   
    full | 否 | string | 是否查看所有信息，如果为空则只返回server的host | 默认不查看所有信息  
    simple | 否 | string | 是否返回简单的信息 | 

请求示例:  
```
type: get
url: http://192.168.0.212:8081/druid/coordinator/v1/servers
```
结果示例:
```json
[
    "192.168.0.212:8083"
]
```

- `/druid/coordinator/v1/servers/{serverName}`  
**基本信息**   
接口说明: 获取指定server的信息  
请求方式: GET  
请求地址: `/druid/coordinator/v1/servers/{serverName}`  
响应类型: application/json  
数据类型: 无   
请求参数: 
    参数名 | 是否必须 | 类型 | 描述  | 默认值 
    ---- | ----- | --- | --- | ---- |   
    serverName | 是 | string | server的名称 |  
    simple | 否 | string | 是否返回简单的信息（不返回segment的信息） | 默认不返回简单的信息

请求示例:  
```
type: get 
url: `http://192.168.0.212:8081/druid/coordinator/v1/servers/192.168.0.212:8083?simple=true`  
```
结果示例:
```json
{
    "host": "192.168.0.212:8083",
    "tier": "_default_tier",
    "type": "historical",
    "priority": 0,
    "currSize": 16618205542,
    "maxSize": 130000000000
}
```

- `/druid/coordinator/v1/servers/{serverName}/segments`  
**基本信息**   
接口说明: 获取指定server上所有的段的信息  
请求方式: GET  
请求地址: `/druid/coordinator/v1/servers/{serverName}/segments`  
响应类型: application/json  
数据类型: 无   
请求参数: 
    参数名 | 是否必须 | 类型 | 描述  | 默认值 
    ---- | ----- | --- | --- | ---- |   
    serverName | 是 | string | server的名称 |   
    full | 否 | string | 是否查看所有信息 | 默认不查看所有信息，只返回段id

请求示例:  
```
type: get 
url: http://192.168.0.212:8081/druid/coordinator/v1/servers/192.168.0.212:8083/segments?full=true  
```
结果示例:
```json
[
    {
        "dataSource": "userinfo",
        "interval": "2017-07-30T00:00:00.000Z/2017-07-31T00:00:00.000Z",
        "version": "2017-08-01T04:58:26.782Z",
        "loadSpec": {
            "type": "local",
            "path": "/data1/druid/storage/userinfo/2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z/2017-08-01T04:58:26.782Z/9/index.zip"
        },
        "dimensions": "",
        "metrics": "",
        "shardSpec": {
            "type": "numbered",
            "partitionNum": 9,
            "partitions": 0
        },
        "binaryVersion": 9,
        "size": 415762,
        "identifier": "userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z_9"
    }
]
```

- `/druid/coordinator/v1/servers/{serverName}/segments/{segmentId}`  
**基本信息**   
接口说明: 获取指定server上指定的段的详细信息  
请求方式: GET  
请求地址: `/druid/coordinator/v1/servers/{serverName}/segments/{segmentId}`  
响应类型: application/json  
数据类型: 无   
请求参数: 
    参数名 | 是否必须 | 类型 | 描述  | 默认值 
    ---- | ----- | --- | --- | ---- |   
    serverName | 是 | string | server的名称 |   
    segmentId | 是 | string | 段id | 

请求示例:  
```
type: get 
url: http://192.168.0.212:8081/druid/coordinator/v1/servers/192.168.0.212:8083/segments/userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z_9 
```
结果示例:
```json
{
    "dataSource": "userinfo",
    "interval": "2017-07-30T00:00:00.000Z/2017-07-31T00:00:00.000Z",
    "version": "2017-08-01T04:58:26.782Z",
    "loadSpec": {
        "type": "local",
        "path": "/data1/druid/storage/userinfo/2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z/2017-08-01T04:58:26.782Z/9/index.zip"
    },
    "dimensions": "",
    "metrics": "",
    "shardSpec": {
        "type": "numbered",
        "partitionNum": 9,
        "partitions": 0
    },
    "binaryVersion": 9,
    "size": 415762,
    "identifier": "userinfo_2017-07-30T00:00:00.000Z_2017-07-31T00:00:00.000Z_2017-08-01T04:58:26.782Z_9"
}
```

---

## 21. `TiersResource`

>class : io.druid.server.http.TiersResource

- `/druid/coordinator/v1/tiers`  
**基本信息**   
接口说明: 获取所有的tier的信息  
请求方式: GET  
请求地址: `/druid/coordinator/v1/tiers`  
响应类型: application/json  
数据类型: 无   
请求参数: 
    参数名 | 是否必须 | 类型 | 描述  | 默认值 
    ---- | ----- | --- | --- | ---- |   
    simple | 否 | string | 是否返回简单的信息 | 

请求示例:  
```
type: get 
url: http://192.168.0.212:8081/druid/coordinator/v1/tiers  
```
结果示例:
```json
[
    "_default_tier"
]
```

- `/druid/coordinator/v1/tiers/{tierName}`  
**基本信息**   
接口说明: 获取tier为指定tier的数据源的信息  
请求方式: GET  
请求地址: `/druid/coordinator/v1/tiers/{tierName}`  
响应类型: application/json  
数据类型: 无   
请求参数: 
    参数名 | 是否必须 | 类型 | 描述  | 默认值 
    ---- | ----- | --- | --- | ---- |   
    tierName | 是 | string | tier的名字 |
    simple | 否 | string | 是否返回简单的信息，如果是，则返回数据源的区间的名称及区间中段的记录数和段的数量 | 默认只返回数据源的名称

请求示例:  
```
type: get 
url: `http://192.168.0.212:8081/druid/coordinator/v1/tiers/_default_tier?simple=true`   
```
结果示例:
```json
{
    "com_HyoaKhQMl_project_SkIqvwtOb": {
        "2017-01-01T00:00:00.000Z/2017-01-02T00:00:00.000Z": {
            "size": 94724,
            "count": 2
        }
    },
    "test1": {
        "2017-06-16T00:00:00.000Z/2017-06-17T00:00:00.000Z": {
            "size": 313386,
            "count": 1
        },
        "2017-06-15T00:00:00.000Z/2017-06-16T00:00:00.000Z": {
            "size": 80654,
            "count": 1
        }
    }
}
```
---

## 22. `HMasterResource`
> class : `io.druid.hyper.server.HMasterResource`

- `/druid/hmaster/v1/leader`  
**基本信息**   
接口说明:获取leader信息           
请求方式:GET  
请求地址:`/druid/hmaster/v1/leader`  
响应类型:application/json    
数据类型:无      
请求参数:无

- `/druid/hmaster/v1/loadstatus`  
**基本信息**   
接口说明:获取数据段的加载状态           
请求方式:GET  
请求地址:`/druid/hmaster/v1/loadstatus`  
响应类型:application/json    
数据类型:\*\/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
     simple | 否 | string | 查看可用的数据段(不包括副本) | 
     full | 否 | string | 查看所有的数据段(包括副本) | 

- `/druid/hmaster/v1/loadqueue`  
**基本信息**   
接口说明:获取待加载和待抛弃(drop)的数据段           
请求方式:GET  
请求地址:`/druid/hmaster/v1/loadqueue`  
响应类型:application/json    
数据类型:\*\/\*      
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    ---- | ----- | --- | --- | ---- |    
     simple | 否 | string | 查看待加载和待抛弃的数据段数量和大小 | 
     full | 否 | string | 查看待加载和待抛弃的数据段的详细信息 | 

---
## 23. `IngestionResource`
> class : `io.druid.hyper.server.IngestionResource`

- `/druid/regionServer/v1/push`  
**基本信息**   
接口说明:更新记录           
请求方式:POST  
请求地址:`/druid/regionServer/v1/push`  
响应类型:application/json    
数据类型:application/json       
请求参数:
    参数名 | 是否必须 | 类型 | 描述  | 默认值
    {Body数据} | 是 | json_string | 要更新的记录的json字符串 |