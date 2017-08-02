# 集群版部署说明

单机版的部署说明请见 [单机版部署](stand-alone.md)  

## 第一步：组建网络和分配资源，即在选择北京3区-A申请私有网络和公网IP
1. 申请私有网络：进入左侧列表的“计算与网络->私有网络”，点击创建，在弹出框输入自定义名称提交即可完成私有网络的申请。
![](/assets/qingcloud/stand-alone/sa-1.png)

2. 申请公网IP：进入左侧列表的“计算与网络->公网IP”，点击申请，在弹出框中输入自定义名称，根据数据量的大小来调整带宽上限，数果建议4Mbps。点击提交即可完成公网IP的申请。
![](/assets/qingcloud/stand-alone/sa-2.png)
 
## 第二步：预配置一个专属网络，即创建一个VPC网络并连接私有网络
1. 创建VPC网络：进入左侧列表的“计算与网络->VPC网络”，点击创建VPC网络，在弹出框中填写自定义的名称，选择网络的地址范围。点击提交即可完成一个VPC网络的创建。
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
    * 版本选择集群版
    * 主节点的CPU，数果建议选择4核以上
    * 主节点的内存，数果建议选择16G以上
    * 主节点的自定义磁盘容量, 根据数据量的大小来调整磁盘的容量大小，数果建议使用500G以上的磁盘
    * 从节点的CPU，数果建议选择4核以上
    * 从节点的内存，数果建议选择16G以上
    * 从节点的数量选择2
    * 从节点的自定义磁盘容量, 根据数据量的大小来调整磁盘的容量大小，数果建议使用500G以上的磁盘   
    * 选择创建好的私有网络
 
  ![](/assets/qingcloud/cluster/c-1.png)
![](/assets/qingcloud/cluster/c-2.png)
 
3. 当“创建资源”的提示消失，大概10分钟以内完成安装，复制角色为主节点的内网IP用于下一步设置端口转发

 ![](/assets/qingcloud/cluster/c-3.png)

    这时需要左侧列表的“计算与网络->VPC网络”中，进入创建好的VPC网络详情界面中，点击切换到管理配置界面中，根据下方表格的参数进行添加端口转发的规则。

| 名称 | 协议 | 源端口 | 内网IP | 内网端口 | 作用 |
| ------ | ------ | ------ | ------ |------ |------ |
| Alaska | tcp | 8080 | 192.168.0.5 | 8080 | 配置Alaska |	
				
  ![](/assets/qingcloud/cluster/c-4.png)

完成端口转发设置后，点击应用修改。

  ![](/assets/qingcloud/cluster/c-5.png)

使用谷歌浏览器打开网址： http://公网IP:8080 ，进入Alaska界面查看astro安装在哪台主机上，找到它的IP地址用于设置端口转发规则。
Alaska账号：admin  密码：admin  
Alaska账号修改密码见[Alaska修改密码](#password)

   ![](/assets/qingcloud/cluster/c-6.png)
     ![](/assets/qingcloud/cluster/c-7.png)
 

这时需要回到左侧列表的“计算与网络->VPC网络”中，进入创建好的VPC网络详情界面中，点击切换到管理配置界面中，根据下方表格的参数继续添加端口转发的规则。

| 名称 | 协议 | 源端口 | 内网IP | 内网端口 | 作用 |
| ------ | ------ | ------ | ------ |------ |------ |
| Nginx | tcp | 80 | 192.168.0.3 | 80 | 配置网关
| Astro | tcp | 8000 | 192.168.0.3 | 8000 | 配置前端界面
| Astro_rely | tcp | 8887 | 192.168.0.3 | 8887 | 配置SDK埋点的socket传输数据
| Tindex | tcp | 8090 | 192.168.0.3 | 8090 | 配置后端程序，查看日志和TASK

![](/assets/qingcloud/cluster/c-8.png)

 

完成端口转发设置后，点击应用修改。

 ![](/assets/qingcloud/cluster/c-9.png)

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
| Alaska | 8080 | 8080 | （可不填） |

  ![](/assets/qingcloud/stand-alone/sa-13.png) 
 

c）完成防火墙规则的配置后，点击应用修改。

   ![](/assets/qingcloud/cluster/c-11.png) 

 

使用谷歌浏览器打开网址： http://公网IP:8080 ，回到Alaska界面修改服务的配置
1. 选择Astro-sugo，切换到配置的界面，展开“高级 astro-site”选项卡

    ![](/assets/qingcloud/cluster/c-12.png) 

1）	将site.collectGateway下的参数改成对应的公网IP（可直接复制Alaska的地址）：http://公网IP。

2）	site.sdk_ws_url下的参数改成对应的公网IP（可直接复制Alaska的地址），端口不变：ws://公网IP:8887。

3）	修改完成后点击保存。

  ![](/assets/qingcloud/cluster/c-13.png) 
4）修改完成后点击保存，重启Astro_sugo的所有服务

 ![](/assets/qingcloud/cluster/c-14.png) 


2.	选择DruidIO-sugo，切换到配置的界面，展开“高级 common.runtime”选项卡，修改druid.license.signature下的参数为数果提供的后端密钥(此密钥需要在下一步的操作指引中进入应用的主界面中联系数果工作人员申请，详情见[最后一步，获取密钥](#acquire))，修改完成后点击保存，重启DruidIO-sugo的所有服务

 ![](/assets/qingcloud/cluster/c-15.png) 
  ![](/assets/qingcloud/cluster/c-16.png) 

最后一步： <span id = "acquire"></span>进入应用的主界面。使用谷歌浏览器打开网址： http://公网IP:8000。此时复制产品注册码联系数果工作人员获取后端的密钥，产品序列号和账号密码即可免费使用产品30天。 
 ![](/assets/qingcloud/cluster/c-17.png) 
![](/assets/qingcloud/cluster/c-18.png) 
 
 
## 关闭应用
根据业务情况，当遇到不需要使用应用的时候，可以选择关闭集群节省消费。
 
![](/assets/qingcloud/cluster/c-19.png) 

## 启动应用
 ![](/assets/qingcloud/cluster/c-20.png) 

 
## 扩展集群容量
当资源不能满足业务需求时，可以扩展集群的节点。有两种扩展方式，一是横向扩展，增加集群的节点数量；二是纵向扩展，增加集群节点的资源容量。

### 方式1：横向扩展
1. 进入“AppCenter->集群列表”，选择进入需要扩展的应用集群详情页。点击新增从节点，可不填写名称进行提交创建。 
 ![](/assets/qingcloud/cluster/c-21.png) 
2. 当“添加资源节点”的提示消失后，大概需要2分钟即可完成创建。
3. 使用谷歌浏览器打开网址： http://公网IP:8080 ，回到Alaska界面，进入新增加的主机详情
  ![](/assets/qingcloud/cluster/c-22.png) 
4. 在主机中点击增加，选择组件进行增加主机扩展，如选择Kafka Broker。
  ![](/assets/qingcloud/cluster/c-23.png) 
5. 安装组件Kafka Broker成功后，回到主界面的 Kafka-sugo进行服务重启，重启后则完成了对组件Kafka Broker的机器扩展。
  ![](/assets/qingcloud/cluster/c-24.png) 



### 方式2：纵向扩展
1. 在左侧列表中进入“AppCenter->集群列表”，右击应用ID，选择扩容集群。
  ![](/assets/qingcloud/cluster/c-25.png) 
  ![](/assets/qingcloud/cluster/c-26.png) 
1）	扩容主节点，增大主节点主机的CPU，内存和磁盘容量；
2）	扩容从节点，增大两台从节点主机的CPU，内存和磁盘容量。

2. 使用谷歌浏览器打开网址： http://公网IP:8080 ，能正常进入并管理Alaska即表示完成扩容没有问题。

  ![](/assets/qingcloud/cluster/c-27.png) 
3. 查看集群详情，主节点的配置已经完成扩容。

   ![](/assets/qingcloud/cluster/c-28.png) 

## 删除并恢复集群

删除集群：在左侧列表进入“AppCenter->集群列表”，在列表中勾选需要删除的应用进行删除操作。

  ![](/assets/qingcloud/cluster/c-29.png) 

恢复集群：在左侧列表进入“管理->回收站”，选择“功能分类：AppCenter”，“资源类型：云应用”，找到被删除的集群进行恢复操作。注意删除集群后两个小时以内能恢复集群，超过两个小时后就彻底被清除不能再进行恢复操作。

 ![](/assets/qingcloud/cluster/c-30.png) 
 
## Alaska修改密码 <span id = "password"></span>
1.	进入管理界面
 ![](/assets/qingcloud/cluster/p-1.png) 
2. 点击进入用户管理，找到admin账号进入详情
 ![](/assets/qingcloud/cluster/p-2.png) 
3. 点击Change Password，在弹出窗中完成密码的修改即可。
 ![](/assets/qingcloud/cluster/p-3.png) 
