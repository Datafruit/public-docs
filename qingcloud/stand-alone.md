# 单机版部署说明

集群版的部署说明请见 [集群版部署](cluster.md)  

## 第一步：组建网络和分配资源，即在选择北京3区-A申请私有网络和公网IP  

1. 申请私有网络：进入左侧列表的“计算与网络->私有网络”，点击创建，在弹出框输入自定义名称提交即可完成私有网络的申请。  

![](/assets/qingcloud/stand-alone/sa-1.png)  

2. 申请公网IP：进入左侧列表的“计算与网络->公网IP”，点击申请，在弹出框中输入自定义名称，根据数据量的大小来调整带宽上限，数果建议4Mbps。点击提交即可完成公网IP的申请。  

![](/assets/qingcloud/stand-alone/sa-2.png)
 
## 第二步：预配置一个专属网络，即创建一个VPC网络并连接私有网络  

1. 创建VPC网络：进入左侧列表的“计算与网络->VPC网络”，点击创建VPC网络，在弹出框中填写自定义的名称，选择网络的地址范围，数果建议使用默认的地址。点击提交即可完成一个VPC网络的创建。 

![](/assets/qingcloud/stand-alone/sa-3.png)
 
2. 连接私有网络：点击上述创建好的VPC网络进入详情页，进行绑定公网IP的操作，点击编辑在弹出窗中选择创建好的公网IP，提交即可。 

![](/assets/qingcloud/stand-alone/sa-4-1.png)
 
3. 点击创建一个私有网络，在弹出框中选择连接已有私有网络，选择已创建的私有网络并提交完成网络的连接。

![](/assets/qingcloud/stand-alone/sa-4.png)
 
4. 提交后需要点击右上角的应用修改，保存配置。

 
## 第三步：部署“数果SUGO-TSA时序多维分析”应用  

1. 找到应用：在左侧“AppCenter->全部应用”中搜索“数果SUGO-TSA时序多维分析”找到应用，点击进入应用详情。

 ![](/assets/qingcloud/stand-alone/sa-5.png)

2. 点击“部署到QingCloud”，进行各项安装应用的操作。

    1）选择区域部署实例，数果建议选择北京3区-A
  ![](/assets/qingcloud/stand-alone/sa-6.png)
    2）	填写各项配置, 然后提交等待安装完成：
    * 版本选择单机版
    * 自定义磁盘容量, 根据数据量的大小来调整磁盘的容量大小，数果建议使用500G以上的磁盘
    * 选择创建好的私有网络
    * 填写创建好的公网IP（进入左侧列表的“计算与网络->公网IP”中复制地址）

  ![](/assets/qingcloud/stand-alone/sa-7.png)
   ![](/assets/qingcloud/stand-alone/sa-8.png)
    
3. 当“创建资源”的提示消失，大概5分钟以内完成安装，复制内网IP用于下一步设置端口转发

  ![](/assets/qingcloud/stand-alone/sa-9.png)

  这时需要左侧列表的“计算与网络->VPC网络”中，进入创建好的VPC网络详情界面中，点击切换到管理配置界面中，根据下方表格的参数进行添加端口转发的规则。

| 名称 | 协议 | 源端口 | 内网IP | 内网端口 | 作用 |
| ------ | ------ | ------ | ------ |------ |------ |
| Nginx | tcp | 80 | 192.168.0.2 | 80 | 配置网关
| Astro | tcp | 8000 | 192.168.0.2 | 8000 | 配置前端界面
| Astro_rely | tcp | 8887 | 192.168.0.2 | 8887 | 配置SDK埋点的socket传输数据
| Tindex | tcp | 8090 | 192.168.0.2 | 8090 | 配置后端程序，查看日志和TASK
				
 ![](/assets/qingcloud/stand-alone/sa-10.png) 


完成端口转发设置后，点击应用修改。

   ![](/assets/qingcloud/stand-alone/sa-11.png) 

接着，到左侧列表的“安全->防火墙”中设置防火墙。如果没有创建好的防火墙，请创建一个。若已有，可直接修改配置。

a）创建防火墙：

  ![](/assets/qingcloud/stand-alone/sa-12.png) 


b）根据下方表格的参数添加防火墙的规则配置，在此过程中可能会出现创建的规则还没有展示在界面上，无需担心可继续创建，当完成点击应用修改后，这些创建的规则会生成出来：

| 名称 | 起始端口 | 结束端口 | 源IP |
| ------ | ------ | ------ | ------ |
| Nginx | 80 | 80 | （可不填） |
| Astro | 8000 | 8000 | （可不填） |
| Astro_rely | 8887 | 8887 | （可不填） |
| Tindex | 8090 | 8090 | （可不填） |

  ![](/assets/qingcloud/stand-alone/sa-13.png) 
 

c）完成防火墙规则的配置后，点击应用修改。

![](/assets/qingcloud/stand-alone/sa-14.png) 

最后一步：进入应用的主界面。使用谷歌浏览器打开网址： `http://公网IP:8000`。
此时复制产品注册码联系数果工作人员获取产品序列号和帐号密码即可免费使用产品30天。

![](/assets/qingcloud/stand-alone/sa-15.png) 
![](/assets/qingcloud/stand-alone/sa-16.png) 

 

 
## 关闭应用
根据业务的情况，当遇到不需要使用应用的时候，可以选择关闭应用节省消费。

 ![](/assets/qingcloud/stand-alone/sa-17.png) 


## 启动应用
  ![](/assets/qingcloud/stand-alone/sa-18.png) 


## 扩展节点容量
当资源不能满足业务需求时，可以增加集群节点的资源容量。

1. 在左侧列表中进入“AppCenter->集群列表”，右击应用ID，选择扩容集群，单机版的只能对磁盘容量进行扩容。

![](/assets/qingcloud/stand-alone/sa-19.png) 
![](/assets/qingcloud/stand-alone/sa-20.png) 
 

2. 查看集群详情，节点的配置已经完成扩容。

![](/assets/qingcloud/stand-alone/sa-22.png)  

## 删除并恢复集群

删除集群：在左侧列表进入“AppCenter->集群列表”，在列表中勾选需要删除的应用进行删除操作。

![](/assets/qingcloud/stand-alone/sa-23.png)  
 

恢复集群：在左侧列表进入“管理->回收站”，选择“功能分类：AppCenter”，“资源类型：云应用”，找到被删除的集群进行恢复操作。注意删除集群后两个小时以内能恢复集群，超过两个小时后就彻底被清除不能再进行恢复操作。

 ![](/assets/qingcloud/stand-alone/sa-24.png)  
