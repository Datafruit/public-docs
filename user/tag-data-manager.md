# 标签数据管理

## 标签数据管理的意义
&emsp;&emsp;标签数据管理主要是管理标签项目的数据。

&emsp;&emsp;通过标签数据管理，可以做到：
* 利用csv/txt文件导入标签数据。
* 为部分用户更新标签值。
* 计算标签值。

## 标签体系管理的功能介绍  
  * 「标签数据导入」：以文件的形式导入标签数据。
  * 「用户群定义标签」：对选定的用户群更新标签值。
  * 「SQL计算标签」：编写SQL语句计算标签值。


## 标签体系管理的操作介绍  

1. 标签数据导入

![](/assets/user/标签数据管理-标签数据导入1.png)

![](/assets/user/标签数据管理-标签数据导入2.png)

2. 用户群定义标签

![](/assets/user/标签数据管理-用户群定义标签1.png)

![](/assets/user/标签数据管理-用户群定义标签2.png)

![](/assets/user/标签数据管理-用户群定义标签3.png)

3. SQL计算标签

![](/assets/user/标签数据管理-SQL计算标签1.png) 

![](/assets/user/标签数据管理-SQL计算标签2.png) 

![](/assets/user/标签数据管理-SQL计算标签3.png) 

# 应用场景

## 场景1：为用户分群更新标签值
    比如现在有一个叫`青少年用户`的分群，想对这个分群的用户在`人群特征`标签维度上写入"年轻人"这个值。
 
    ![](/assets/user/标签数据管理-用户群定义标签demo.png) 

## 场景2：计算标签值
    比如要计算每个用户的投资金额这个标签值。
 
    ![](/assets/user/标签数据管理-SQL计算标签demo.png) 

 


关于用户运营的介绍：
  * [用户运营](user-operation.md)
  * [用户指标](user-quota.md)
  * [场景数据设置](user-operation.md#scene-setting)
  * [用户分群](user-segmentation.md)
  * [用户行为轨迹](user-segmentation.md#behavior-trace)
  * [路径分析](path-analytics.md)
  * [留存分析](retation-analytics.md)
  * [漏斗分析](funnel-analytics.md)
  * [事件分析](event-analytics.md)