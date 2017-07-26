# 查询Overlord、MiddleManager节点

以下以广东数果科技有限公司为例，查询集群中的 `Overlord` 和 `MiddleManager`

## 第一步：通过浏览器登录Ambari后台页面
- 访问地址：
```url
192.168.0.220:8080
```

![](/assets/overlordMiddleManagerGuide/login.png)

用户名及密码均是：`admin`   

## 第二步：查看Druid集群
- 登陆成功后，点击页面左边的 `DruidIO-sugo` ，如图所示：

![](/assets/overlordMiddleManagerGuide/DruidNodeList.png)


## 第三步：查找Overlord节点

- 点击第二步页面中的 `Overlord`

![](/assets/overlordMiddleManagerGuide/click_overlord.png)

- 查找到集群的 `Overlord` 节点如下所示：

![](/assets/overlordMiddleManagerGuide/overlordNode.png)

## 第四步：查找MiddleManager节点

- 点击第二步页面中的 `MiddleManagers`

![](/assets/overlordMiddleManagerGuide/click_middleManagers.png)

- 查找到集群的 `MiddleManager` 节点如下所示：

![](/assets/overlordMiddleManagerGuide/middleManagerNodes.png)
