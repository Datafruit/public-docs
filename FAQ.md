### 1. 创建任务失败，task异常日志如下
```
2017-08-09 20:03:44.613 ERROR [task-runner-0-priority-0] io.druid.indexing.overlord.ThreadPoolTaskRunner - Exception while running task[LuceneKafkaIndexTask{id=lucene_index_kafka_ec_svr_op_data_30d7f3942e79bb3_ckgjmdbd, type=lucene_index_kafka, dataSource=ec_svr_op_data}]
com.metamx.common.ISE: Cannot create task basePersistDirectory[/data2/druidTask/storage/ec_svr_op_data/lucene_index_kafka_ec_svr_op_data_30d7f3942e79bb3_ckgjmdbd]
	at io.druid.segment.indexing.RealtimeTuningConfig.createNewBasePersistDirectory(RealtimeTuningConfig.java:67) ~[druid-server-1.0.0.jar:1.0.0]
	at io.druid.segment.indexing.RealtimeTuningConfig.<init>(RealtimeTuningConfig.java:176) ~[druid-server-1.0.0.jar:1.0.0]
	at io.druid.segment.indexing.RealtimeTuningConfig.<init>(RealtimeTuningConfig.java:144) ~[druid-server-1.0.0.jar:1.0.0]
	at io.druid.indexing.kafka.LuceneKafkaIndexTask.newAppenderator(LuceneKafkaIndexTask.java:855) ~[?:?]
	at io.druid.indexing.kafka.LuceneKafkaIndexTask.run(LuceneKafkaIndexTask.java:281) ~[?:?]
	at io.druid.indexing.overlord.ThreadPoolTaskRunner$ThreadPoolTaskRunnerCallable.call(ThreadPoolTaskRunner.java:436) [druid-indexing-service-1.0.0.jar:1.0.0]
	at io.druid.indexing.overlord.ThreadPoolTaskRunner$ThreadPoolTaskRunnerCallable.call(ThreadPoolTaskRunner.java:408) [druid-indexing-service-1.0.0.jar:1.0.0]
	at java.util.concurrent.FutureTask.run(FutureTask.java:266) [?:1.8.0_91]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142) [?:1.8.0_91]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617) [?:1.8.0_91]
	at java.lang.Thread.run(Thread.java:745) [?:1.8.0_91]
2017-08-09 20:03:44.619 INFO [task-runner-0-priority-0] io.druid.indexing.overlord.TaskRunnerUtils - Task [lucene_index_kafka_ec_svr_op_data_30d7f3942e79bb3_ckgjmdbd] status changed to [FAILED].
2017-08-09 20:03:44.620 INFO [task-runner-0-priority-0] io.druid.indexing.worker.executor.ExecutorLifecycle - Task completed with status: {
  "id" : "lucene_index_kafka_ec_svr_op_data_30d7f3942e79bb3_ckgjmdbd",
  "status" : "FAILED",
  "duration" : 68
}
```

#### 原因：
用户没有权限在目录/data2/druidTask/storage/下创建目录

#### 解决方案：
1. 检查目录是否存在
2. 检查用户是否有创建目录的权限，一般druid用户需要对/data2/druidTask/storage/目录的写权限

### 2. 220环境druid更新部署失败
在ambari界面更新druid后台时报错，错误信息：
```
 File "/usr/lib/python2.6/site-packages/resource_management/core/shell.py", line 291, in _call
    raise Fail(err_msg)
resource_management.core.exceptions.Fail: Execution of 'mv  /opt/apps/druid* /opt/apps/druidio_sugo' returned 1. mv: target `/opt/apps/druidio_sugo' is not a directory
```
更新失败后，没有处理，直接启动服务，发现启动服务失败，ambari上的错误日志如下：
```
  File "/usr/lib/python2.6/site-packages/resource_management/core/providers/system.py", line 87, in action_create
    raise Fail("Applying %s failed, parent directory %s doesn't exist" % (self.resource, dirname))
resource_management.core.exceptions.Fail: Applying File['/opt/apps/druidio_sugo/conf/druid/overlord/supervisor.properties'] failed, parent directory /opt/apps/druidio_sugo/conf/druid/overlord doesn't exist
```
### 原因：
目录/opt/apps/下在更新之前存在druid开头的文件，导致更新时失败
`mv  /opt/apps/druid* /opt/apps/druidio_sugo`目的是用于修改目录名称，但如果有druid开头的文件，则变成了将druid开头的文件移动到/opt/apps/druidio_sugo目录。而此时这个目录并不存在，所以报错。

### 解决方案：
保证更新之前/opt/apps/目录下不存在druid开头的文件。如果存在，则重命名为非druid开头的文件。

### 3. 环境更新后起Task失败
在overlord的console页面上查看对应Task的日志，发现以下错误信息：
```
ERROR: transport error 202: bind failed: Address already in use
ERROR: JDWP Transport dt_socket failed to initialize, TRANSPORT_INIT(510)
JDWP exit error AGENT_ERROR_TRANSPORT_INIT(197): No transports initialized [debugInit.c:750]
FATAL ERROR in native method: JDWP No transports initialized, jvmtiError=AGENT_ERROR_TRANSPORT_INIT(197)
```

根据描述，初步猜测可能是地址已被占用的问题   
接下来登录overlord主机查看overlord的日志，发现当任务地址从`[TaskLocation{host=null, port=-1}]`变为以下地址时`[TaskLocation{host='dev224.sugo.net', port=8102}]`，任务状态就变成falied，可以进一步确定是端口被占用

### 原因：
MiddleManager的配置中多了一项：`-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=6305`以用于调试，它需要占用一个端口。在启动第一个task时会绑定指定的端口，但当第二个任务启动时，也需要绑定该端口，但同一个端口只能被一个进程绑定。


### 解决方案：
1. 登录集群的`ambri`控制台页面
2. 进入`DruidIO-Sugo`的配置项下
3. 选择`高级 middlemanager.config`
4. 把`druid.indexer.runner.javaOpts`配置项中的` -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=6305`删掉，按保存，并重启MiddleManager服务

### 4. 自助分析用户唯一ID在维度栏是查询不出数据
在druid后端的historical.log查看日志报错，错误信息如下：

![](/assets/image/druid内存不足.png)
Not enough dictionary space to execute this query. Try increasing druid.lucene.query.groupBy.maxMergingDictionarySize or enable disk spilling by setting druid.lucene.query.groupBy.maxOnDiskStorage to a positive number.

根据描述，初步猜测可能是druid.lucene.query.groupBy.maxOnDiskStorage的数量不足

### 解决方案：
在对应服务的/opt/apps/druidio_sugo/conf/druid/_common的common.runtime.properties中加入参数druid.lucene.query.groupBy.maxOnDiskStorage=100000000（看具体看情况订数字大小）

### 5. 接数据时内存不足
查相应task的日志，出现以下报错
Java.lang.OutOfMemoryError: GC overhead limit exceeded
![](/assets/image/QQ截图20170817195058.png)

该问题主要是由于历史数据的时间跨度比较大，段聚合的粒度太小，导致段的数量太多，占用太多机器内存。

### 解决方案：
如果历史数据的时间跨度太大，要注意相应调整段聚合粒度的大小，保证段的数量不会过多。

### 6. 任务失败，无法分配segment

task中的错误日志信息如下：  
```
2017-09-12T09:21:30,098 INFO [task-runner-0-priority-0] io.druid.segment.realtime.appenderator.FiniteAppenderatorDriver - Allocate new segment...
2017-09-12T09:21:30,106 INFO [task-runner-0-priority-0] io.druid.indexing.common.actions.RemoteTaskActionClient - Performing action for task[lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_goalffig]: SegmentAllocateAction{dataSource='com_SJ1B_Zq6e_project_BJrOA8HZW', timestamp=2017-09-12T08:16:59.000Z, queryGranularity=NoneGranularity, preferredSegmentGranularity=HOUR, sequenceName='lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_1', previousSegmentId='null'}
2017-09-12T09:21:30,120 INFO [task-runner-0-priority-0] io.druid.indexing.common.actions.RemoteTaskActionClient - Submitting action for task[lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_goalffig] to overlord[http://dev6.bbk.sugo:8090/druid/indexer/v1/action]: SegmentAllocateAction{dataSource='com_SJ1B_Zq6e_project_BJrOA8HZW', timestamp=2017-09-12T08:16:59.000Z, queryGranularity=NoneGranularity, preferredSegmentGranularity=HOUR, sequenceName='lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_1', previousSegmentId='null'}


2017-09-12T09:21:30,501 ERROR [task-runner-0-priority-0] io.druid.indexing.overlord.ThreadPoolTaskRunner - Exception while running task[LuceneKafkaIndexTask{id=lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_goalffig, type=lucene_index_kafka, dataSource=com_SJ1B_Zq6e_project_BJrOA8HZW}]
com.metamx.common.ISE: Could not allocate segment for row with timestamp[2017-09-12T08:16:59.000Z]
	at io.druid.indexing.kafka.LuceneKafkaIndexTask.run(LuceneKafkaIndexTask.java:439) ~[?:?]
	at io.druid.indexing.overlord.ThreadPoolTaskRunner$ThreadPoolTaskRunnerCallable.call(ThreadPoolTaskRunner.java:436) [druid-indexing-service-1.0.0.jar:1.0.0]
	at io.druid.indexing.overlord.ThreadPoolTaskRunner$ThreadPoolTaskRunnerCallable.call(ThreadPoolTaskRunner.java:408) [druid-indexing-service-1.0.0.jar:1.0.0]
	at java.util.concurrent.FutureTask.run(FutureTask.java:266) [?:1.8.0_91]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142) [?:1.8.0_91]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617) [?:1.8.0_91]
	at java.lang.Thread.run(Thread.java:745) [?:1.8.0_91]
2017-09-12T09:21:30,508 INFO [task-runner-0-priority-0] io.druid.indexing.overlord.TaskRunnerUtils - Task [lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_goalffig] status changed to [FAILED].
2017-09-12T09:21:30,509 INFO [task-runner-0-priority-0] io.druid.indexing.worker.executor.ExecutorLifecycle - Task completed with status: {
  "id" : "lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_goalffig",
  "status" : "FAILED",
  "duration" : 704
}
```
overlord服务的日志中可以发现如下异常日志：  
```
2017-09-12 09:21:30.249 INFO [qtp670996243-84][Logger.java:69] - Adding lock on interval[2017-09-12T08:00:00.000Z/2017-09-12T09:00:00.000Z] version[2017-09-12T08:00:04.172Z] for task: lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_goalffig
2017-09-12 09:21:30.398 WARN [qtp670996243-84][Logger.java:82] - Cannot use existing pending segment [com_SJ1B_Zq6e_project_BJrOA8HZW_2017-09-12T07:00:00.000Z_2017-09-12T08:00:00.000Z_2017-09-12T07:00:02.889Z_39] for sequence[lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_1] (previous = []) in DB, does not match requested interval[2017-09-12T08:00:00.000Z/2017-09-12T09:00:00.000Z]
2017-09-12 09:21:30.768 INFO [qtp670996243-92][Logger.java:69] - Generating: http://dev15.bbk.sugo:8091
2017-09-12 09:21:31.094 INFO [Curator-PathChildrenCache-0][Logger.java:69] - Worker[dev15.bbk.sugo:8091] wrote FAILED status for task [lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_goalffig] on [TaskLocation{host='dev15.bbk.sugo', port=8101}]
2017-09-12 09:21:31.094 INFO [Curator-PathChildrenCache-0][Logger.java:69] - Worker[dev15.bbk.sugo:8091] completed task[lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_goalffig] with status[FAILED]
```

### 解决方案：
修改参数lateMessageRejectionPeriod的配置：P30D或更长时间，用于修改sequenceName的hash值lucene_index_kafka_com_SJ1B_Zq6e_project_BJrOA8HZW_883bd68ff7f9654_1

### 7. 接数据时报错 OffsetOutOfRangeException
查看task的日志,出现报错:
```
2017-10-10 16:29:14.879 WARN [task-runner-0-priority-0] io.druid.indexing.kafka.BatchKafkaIndexTask - OffsetOutOfRangeException with message [Offsets out of range with no configured reset policy for partitions: {visitRecord001-0=4247231}], retrying in 30000ms
```

### 解决方案：
在`overlord-IP:8090`界面将对应的Supervisor进行reset，或者使用如下命令
```
curl -X POST -H 'Content-Type:application/json' http://{overlord-IP}:8090/druid/indexer/v1/supervisor/{supervisorId}/reset
```

### 8. 在overlord管理界面（端口为8090的界面）可以看到很多task（但找不到对应的进程，并且druid_tasks数据库表中的记录状态也为false），导致新的任务一直在pendding Tasks列表中。
问题：    
由于某种原因，导致task在创建时就失败了，进程没有启动。数据库表druid_tasks中的记录状态为false。即使删除记录，页面上还是可以看到这条记录。
问题原因：    
可能的一种原因是因为磁盘爆满了，导致启动task进程失败。df -lh检查磁盘使用情况
还有一种原因是启动task进程时权限不足。 查看overlord和middlemanage的日志，根据日志查看对应的错误

### 解决方案：
检查zk上的数据，进入`/opt/apps/zookeeper_sugo`目录，执行`bin/zkCli.sh`命令，进入zk的cli模式下。    
检查`/druid/indexer/status/{middlemanage`的域名，在8090界面可以查看到task所在的middlemanage}目录下是否存在对应的taskid    
比如`ls /druid/indexer/status/dev222.sugo.net:8091`    
是否包含`lucene_index_kafka_com_SJLnjowGe_project_HJuRfqI_G_cgfncmoh`    
如果包含，则删除：    
`rmr /druid/indexer/status/dev222.sugo.net:8091/lucene_index_kafka_com_SJLnjowGe_project_HJuRfqI_G_cgfncmoh`    
 
 ### 9. 加载drop掉的datasource
1. 删除掉drop该datasource的的rule
进入postgres目录，执行指令
`bin/psql -p 15432 -U postgres -d druid -c "delete from druid_rules where datasource = '{datasource_name}'"`
2. 将该datasource的segment的加载状态改为true
进入postgres目录，执行指令
`bin/psql -p 15432 -U postgres -d druid -c "update druid_segments set used = true where datasource = '{datasource_name}'`

### 10. postgres 重用命令
1. 登录
`psql -p 15432 -U username -d dbname`
2. 列所有的数据库
`\l`
3. 切换数据库
`\c dbname`
4. 列出当前数据库下的数据表
`\d`
5. 查看指定表的所有字段
`\d tablename`
6. 退出登录
`\q`
 
 
