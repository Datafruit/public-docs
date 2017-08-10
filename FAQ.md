### 1.创建任务失败，task异常日志如下
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
1.检查目录是否存在
2.检查用户是否有创建目录的权限，一般druid用户需要对/data2/druidTask/storage/目录的写权限

### 2.220环境druid更新部署失败
```
  File "/usr/lib/python2.6/site-packages/resource_management/core/providers/system.py", line 87, in action_create
    raise Fail("Applying %s failed, parent directory %s doesn't exist" % (self.resource, dirname))
resource_management.core.exceptions.Fail: Applying File['/opt/apps/druidio_sugo/conf/druid/overlord/supervisor.properties'] failed, parent directory /opt/apps/druidio_sugo/conf/druid/overlord doesn't exist
```
### 原因：
druid包名命名错误，导致路径下找不到包

### 解决方案：
建平手工更druid包命名
以后可以通过Jenkins打包
