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
1.检查目录是否存在
2.检查用户是否有创建目录的权限，一般druid用户需要对/data2/druidTask/storage/目录的写权限

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
```
Not enough dictionary space to execute this query. Try increasing druid.lucene.query.groupBy.maxMergingDictionarySize or enable disk spilling by setting druid.lucene.query.groupBy.maxOnDiskStorage to a positive number.
```
根据描述，初步猜测可能是druid.lucene.query.groupBy.maxOnDiskStorage的数量不足


### 解决方案：
在对应服务的/opt/apps/druidio_sugo/conf/druid/_common的common.runtime.properties中加入参数druid.lucene.query.groupBy.maxOnDiskStorage=100000000（看具体看情况订数字大小）


![](/image/QQ%E6%88%AA%E5%9B%BE20170817195058.png)


