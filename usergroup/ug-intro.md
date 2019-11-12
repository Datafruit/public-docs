# 用户分群

在数果智能中，用户分群分为用户分群和用户细查两块，用户分群是设置分群和管理分群，用户细查是细查该分群的用户情况，包括用户的行为路径等。

在用户的精细化分析中，常常需要对有某个特定行为的用户群组进行分析和比对，数果智能 的「用户分群」支持将多维度和多指标作为分析条件，有针对性地优化产品，提升用户体验。进入用户分群列表后，可以根据你的需要建立分群。

![](/assets/fenqun/1.png)


# 操作介绍

这里以某电商的用户分群设置为例子，讲述整个用户分群操作的新建、设置、保存、删除和其他等操作。

## 新建用户分群

### **1.点击新建用户分群**

在列表的右上角，点击“新建用户分群”进入用户分群的新建。

![](/assets/fenqun/2.png)

### **2.设置用户分群**

![](/assets/fenqun/3.png)

* 填写标题和备注、数据源、选定用户的时间范围（以下选择的行为、指标和维度均有影响）
* 选择用户维度，注意一般才有UserID或其他能代表用户ID唯一标识的字段
* 用户行为：支持多行为的并行条件，有并且和或者两种关系（需要到场景数据设置中配置用户行为维度的字段）
![](/assets/fenqun/11.png)
  ```
  上述条件中选定的用户具有以下行为：
    在问答页点击了使用指南大于1或者在问答页点击了回顾视频大于1的用户。
  ```
* 指标筛选：支持多指标的并行条件，有并且和或者两种关系
![](/assets/fenqun/12.png)
  ```
  上述条件中选定的用户满足以下指标：
    浏览并点击的次数大于5并且总记录数大于10的用户。
  ```
* 维度筛选：支持多维度的并行条件，有并且和或者两种关系
![](/assets/fenqun/13.png)  
  ```
  上述条件中选定的用户满足以下条件：
    设备品牌是“苹果、华为、小米”其一并且屏幕大小为“2560*1440、1830*1200”其一的用户。
  ```


### **3.保存用户分群**

点击“保存”进入用户分群的保存，即完成用户分群的创建。

## **4.其他操作**

![](/assets/fenqun/4.png)

* 用户详情:查看该用户分群的用户详情
* 查看关联漏斗：查看该用户分群的关联漏斗
* 更新：更新该用户分群的设置
* 删除：删除该用户群


## 用户分群列表
![](/assets/fenqun/14.png)  

* 编辑：在列表进入编辑状态，操作同新建用户分群一样。

* 删除：点击列表的删除按钮，弹出是否删除的对话框，点击“确定”完成用户分群的删除。

* 查看分群情况：点击进入该用户群的用户细查中，可查看该群的所有用户详情。

***


# 应用场景

## 场景一：某电商专项促销活动的分群应用

这里以某电商为例，某电商平台要针对几个热点省份\(广东、浙江、福建和江苏\)的年轻用户做专项促销，需要这部分用户建立不同细分用户群，为此该平台从以往促销活动中抽取一个活动，以在这个活动有提交订单的行为的用户为研究对象，展开目标人群的分组划分。操作如下：

1.该电商平台的运营人员点击“新建用户群‘后进入用户群的参数设置界面；

2.选择“某电商“的数据源，用户维度选择为”UserID“,创建方式默认为过滤条件，时间范围为2016-11-01到2016-11-30的11月份的数据。

![](/assets/fenqun/5.png)

3.选择维度\[国家\]=中国、维度\[省份\]=广东省、维度\[商品大类\]=衣服后，通过设置后完成省份、商品大类（这里以衣服这个大类），维度数据的设置。

![](/assets/fenqun/6.png)

4.选择指标中\[事件名称\]=某促销活动页、 \[动作类型\]=对焦、 \[事件按钮\]=立即购买后，选择大于1后，完成购买用户（有提交至少1单以上的用户）的设置；

![](/assets/fenqun/7.png)

保存后，就建立了符合条件的用户分群。

## 场景二：不同时间段用户购买母婴产品的下单情况的研究

在电商运营中，一天的不同时间段内用户购买行为、购买频次和下单情况也是不同，这里还是以某电商的调研活动为例，某电商要研究不同时间段用户购买母婴产品的下单情况，以便调整某些母婴产品的上架时间和预热时间，于是以某周日的母婴活动促销页为时间点，按照每3小时为时间单位，选择区域为来自浙江的用户，有产生至少1次购买行为的用户为调研对象，来研究不同时间段内的用户情况。

具体的操作如下：

1.该电商平台的运营人员点击“新建用户群‘后进入用户群的参数设置界面；

2.选择“某电商“的数据源，用户维度选择为”UserID“,创建方式默认为过滤条件，时间范围为2017-02-05的整天数据（时间点从00：00：00到23:59:59）。

![](/assets/fenqun/8.png)

3.选择维度\[国家\]=中国、维度\[省份\]=浙江省、维度\[商品大类\]=母婴、维度\[下单时间（小时）\]=18：00到21：00后，通过设置后完成省份、商品大类（这里以母婴这个大类）和时间段（这里是18：00到21：00这个时间段），来完成维度数据的设置。

![](/assets/fenqun/9.png)

4.选择指标分别为\[事件名称\]=商品详情、促销活动和提交订单页、 \[动作类型\]=点击、浏览和点击、 \[事件按钮\]=立即购买、某母婴促销活动和立即提交后，都选择大于1后，筛选出点击商品详情中立即购买至少1次以上、浏览某母婴促销活动至少1次以上和提交订单页中立即提交至少1次以上这部分真正有购买行为的用户；

![](/assets/fenqun/10.png)

保存后，就建立了符合条件的用户分群。
