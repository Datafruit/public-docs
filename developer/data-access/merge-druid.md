# 将Druid中datasource的分段碎片进行合并

> 前提：
  > 1. Druid中已存在相应的datasource　
  > 2. datasource中的记录有大于等于２的分段碎片（shard）
  

以下以广东数果科技有限公司为例，展示合并datasource全过程操作指引：

## 第一步：查找在druid中的目标datasource

1. 在`http://192.168.0.222:8081/#/datasources`页面上查找要合并的datasource。
例如要合并的datasource是`test061901`

![](/assets/datamerge/before__test061901.png)

> 注意：上图红框内的数字”２“代表该条记录包含两个分段碎片。

## 第二步: 创建、编辑、保存LuceneMergeTask.json文件说明

![](/assets/datamerge/LuceneMergeTask.png)

## 第三步：执行命令上传json文件,创建Task任务

- 在命令行中使用`cd`命令进入第二步创建的json文件所在的目录
- 使用`curl`命令上传json文件：

  ```shell
  curl -X 'POST' -H 'Content-Type:application/json' -d @LuceneMergeTask.json http://{OverloadIP}:8090/druid/indexer/v1/task
  ```

   > **OverloadIP:** druid的overload节点ip地址（本例使用的是：192.168.0.220）

   > **LuceneMergeTask.json** task配置文件，详见下文

## 第四步：进入overload服务的Task列表页面，查看Task日志信息，观察Task合并情况。

![](/assets/datamerge/task_list.png)

- 访问地址：
  ```javascript
  http://{OverloadIP}:8090/console.html
  ```

  > **OverloadIP:** druid的overload节点ip地址

## 第五步：在需要停止task时，可以发送如下http post请求停止task任务

  ```shell
  curl -X 'POST' -H 'Content-Type:application/json' http://{OverloadIP}:8090/druid/indexer/v1/task/{taskId}/shutdown
  ```

  > **OverloadIP:** druid的overload节点ip地址

  > **taskId:** 在`http://{OverloadIP}:8090/console.html`task详细页面对应 **id** 列的信息

## 第六步：查看合并结果

![](/assets/datamerge/merge_result.png)

可以看到，目标datasource的每条记录都变成只有一个分段碎片了

### LuceneMergeTask.json详细配置如下：
```javascript
{
  "type":"lucene_merge",
  "dataSource": "test061901",  
  "interval": "2001-01-01/2020-01-01",
  "triggerMergeCount": 2,
  "context": {
    "debug": true
  }   
}
```

字段 | 描述　
--- | ---
type | 指定任务类型
dataSource | 要合并的datasource名称
interval　| 需要进行合并的时间间隔，在间隔范围外的不进行合并
triggerMergeCount |  设定针对多少个分段碎片进行合并，默认为２
context　| 指定任务的其它环境参数


