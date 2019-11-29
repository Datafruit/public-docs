## SDK初始化流程图
![](/assets/visualization-sdk-flow/sdk-init.png)
### 1.请求sdk初始化参数
APP会向埋点服务器请求初始化参数，埋点服务器会向APP发送4个字段，分别是:latestEventBindingVersion(最新的事件版本),latestDimensionVersion(最新的维度版本),isUpdateConfig(是否强制拉取维度),isSugoInitialize(是否初始化sdk)。
### 2.是否初始化sdk
当APP收到isSugoInitialize参数时，如果该参数为true时，初始化sdk，否则直接退出sdk初始化，不执行任何动作。
### 3.拉取维度配置
当sdk初始化时，会先通过isUpdateConfig字段是否强制拉取埋点服务器的维度配置。isUpdateConfig为true时，会强制拉取服务器的维度配置；当isUpdateConfig为false时，会通过latestDimensionVersion字段与本地存储的版本号相比较，如果版本号一致，使用本地缓存的维度配置，如果版本号不一致，会拉取服务器的维度配置。
### 4.拉取埋点事件配置
当sdk初始化时，会先通过isUpdateConfig字段是否强制拉取埋点服务器的埋点事件配置。isUpdateConfig为true时，会强制拉取服务器的埋点事件配置；当isUpdateConfig为false时，会通过latestEventBindingVersion字段与本地存储的版本号相比较，如果版本号一致，使用本地缓存的埋点事件配置，如果版本号不一致，会拉取服务器的埋点事件配置。



## SDK 埋点事件逻辑流程图
![](/assets/visualization-sdk-flow/snapshot-touch.png)
### 1.通过埋点信息绑定控件
埋点事件会通过底层方法，绑定相对应的控件。
### 2.用户触发已绑定的埋点控件
当用户点击已绑定控件时，会触发埋点控件的回调，执行相对应的绑定逻辑。
### 3.生成埋点事件，保存到本地
响应了埋点事件的回到后，会生成一个埋点事件，埋点事件首先会保存到本地，等待上报。
### 4.满足一定的条件，发送埋点事件数据
当定时器触发或者存储数据的数目达到一定时，会批量上报数据到埋点服务器后台。
### 5.返回发送状态
当有数据上报时，埋点服务器会把存储的状态返回来给APP。
### 6.发送后数据处理
当状态为true时，会删除本地的数据，当状态为false时，会继续存储数据，等待下一次触发。
