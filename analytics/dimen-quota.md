# 维度功能 <span id = "dimen"></span>

&emsp;&emsp;维度是我们接入数据的每一个字段名称，指研究对象的描述性属性或特征，就是我们常说的字段。维度区域中罗列了维度管理中的所有维度。维度介绍具体详情见[维度管理](/dimension-management.md)。

## 维度数值类型
&emsp;&emsp;SUGO-BTSA支持多类型数值处理：


&emsp;&emsp;![](/assets/data-analysis/dimen-1.png)&emsp;表示Float浮点类型，精确到小数点后两位。

&emsp;&emsp;![](/assets/data-analysis/dimen-2.png)&emsp;表示Int整数类型，存储32位整数；表示Long长整数类型，存储64位整数。

&emsp;&emsp;![](/assets/data-analysis/dimen-3.png)&emsp;表示文本类型，存储大量的字符信息。常用于存储日志信息。需要注意：文本类型的字段不能作为筛选条件与分组维度使用，可在源数据列表中查看该字段上报的数据。

&emsp;&emsp;![](/assets/data-analysis/dimen-4.png)&emsp;表示时间类型，精确到时分秒的选择。

&emsp;&emsp;![](/assets/data-analysis/dimen-5.png)&emsp;表示字符串类型。

## 维度的拖拽分析
&emsp;&emsp;您可以将维度拖拽到筛选栏进行过滤设置；拖拽到维度栏进行分组设置，拖拽到数值栏进行统计计算。具体介绍详情见[查询条件设置](query-condition.md)
![](/assets/data-analysis/dimen-6.gif)
## 维度的管理
### 维度的顺序管理
&emsp;&emsp;您可以调整常用/不常用的维度排列的顺序，也可以隐藏不使用的维度。
点击“编辑维度顺序”进入管理操作状态：
1. 调整顺序：拖动维度进行重新排序；点击某个维度的"到顶部”“到底部”的按钮可以快速置顶置底。
2. 切换维度的可见：点击某个维度的“可见”按钮，快速切换为不可见。退出管理状态后，不可见的维度已被隐藏。

    ![](/assets/data-analysis/dimen-7.gif)

### 开启搜索维度的功能
&emsp;&emsp;您可以通过搜索功能，在众多维度中快速查找到需要的维度。点击“搜索”开启搜索维度的功能，输入维度关键词后相关的维度会在其下方显示。

![](/assets/data-analysis/dimen-8.gif)
## 按标签过滤维度
&emsp;&emsp;您可以使用标签进行管理您的维度，可快速找到同类标签的维度。
点击“按标签筛选”，在弹出的选项卡中点击某个标签，可快速找到该标签下的所有维度。若不使用标签，可再次点击选中的标签进行取消选中的操作。

&emsp;&emsp;维度的标签管理具体详情见[维度管理](/dimension-management.md)

![](/assets/data-analysis/dimen-9.gif)


***

# <a id="指标功能" href="指标功能"></a> 指标功能  
&emsp;&emsp;指标是我们进行统计总体数量的定义，比如“点击的数量”，“用户的数量”。通过指标管理功能⾃定义简单或者复合指标将会呈现在该区域，⽅便⽤户灵活分析更多更复杂的指标。
指标介绍具体详情见[指标管理](/indicator-management.md)。


## 指标的拖拽分析
您可以将指标拖拽到数值栏进行统计计算。具体介绍详情见[查询条件设置](query-condition.md)

![](/assets/data-analysis/quota-1.gif)

## 指标的管理
### 指标的顺序管理
&emsp;&emsp;您可以调整常用/不常用的指标排列的顺序，也可以隐藏不使用的指标。
点击“编辑指标顺序”进入管理操作状态：
1. 调整顺序：拖动指标进行重新排序；点击某个指标的“到顶部”“到底部”的按钮可以快速置顶置底。
2. 切换指标的可见：点击某个指标的“可见”按钮，快速切换为不可见。退出管理状态后，不可见的指标已被隐藏。

    ![](/assets/data-analysis/quota-2.gif)
### 开启搜索指标的功能
&emsp;&emsp;您可以通过搜索功能，在众多指标中快速查找到需要的指标。
点击“搜索”开启搜索指标的功能，输入指标关键词后相关的指标会在其下方显示。

![](/assets/data-analysis/quota-3.gif)

### 创建指标
&emsp;&emsp;当您在分析的过程中需要创建更多的指标，那么可以点击快速进入指标管理的界面，进行创建指标的操作。创建指标具体详情见[指标管理](/indicator-management.md)

![](/assets/data-analysis/quota-4.png)
### 按标签过滤指标
您可以使用标签进行管理您的指标，可快速找到同类标签的指标。
点击“按标签筛选”，在弹出的选项卡中点击某个标签，可快速找到该标签下的所有指标。若不使用标签，可再次点击选中的标签进行取消选中的操作。
维度的标签管理具体详情见[维度管理](/dimension-management.md)

![](/assets/data-analysis/quota-5.gif)

关于多维分析的介绍：
* [多维分析](data-index.md)
* [界面介绍](data-index.md#intro)
* [查询条件设置](query-condition.md)
* [图表类型](chart-intro.md)