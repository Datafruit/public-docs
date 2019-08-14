# 下载文件


 - 下载地址：
  [sugo-wx-mini.program.min.js](http://tas.sugo.io/_bc/sugo-sdk-js/libs/sugo-wx-mini.program.min.js) (右键另存为)

# App init参数配置

```javascript
const sugoio = require('./util/wx-mini-sdk.js') //引入sugoio

App({
  onLaunch: function () {
    sugoio.App.init({
      "project_id": "com_H1bIzqK2SZ_project_HyAKaKC5Z", //项目ID
      "token": "2c170b6be6bb15521bd8560a41f8c849" // 项目TOKEN
    })
    console.log('App Launch')
  })
```

* **${appid}：** 为应用TOKEN。
* **${project_id}：** 为项目ID。

## 用户自定义维度

系统大数据 的多维分析工具本身提供了例如 “访问来源”，“城市”,“操作系统"，”浏览器“等等这些维度。这些维度都可以和用户创建的指标进行多维的分析。但是往往不能满足用户对数据多维度分析的要求，因为每个公司的产品都有各自的用户维度，比如客户所服务的公司，用户正在使用的产品版本等等。 系统大数据 为了能够让数据分析变得更加的灵活，我们在 JS SDK 中提供了用户自定义维度的API接口:

### **自定义事件上报(代码埋点上报)**

第一次接入系统大数据时，建议先追踪 3~5 个关键的事件，只需要几行代码，便能体验 系统大数据 的分析功能。例如：

* 图片社交产品，可以追踪用户浏览图片和评论事件。
* 电商产品，可以追踪用户注册、浏览商品和下订单等事件。

系统大数据 SDK 初始化成功后，即可以通过 sugoio.track\(event\_name, \[properties\], \[callback\]\) 记录事件：

* **event_name**: string，必选。表示要追踪的事件名。
* **properties**: object，可选。表示这个事件的属性。
* **callback**: function，可选。表示已经发送完数据之后的回调。

```javascript
  // 追踪浏览商品事件
  sugoio.track('ViewProduct', {
    'ProductId': 123456，
    'ProductCatalog': "Laptop Computer",
    'ProductName': 'MacBook Pro',
    'ProductPrice': 888.88,
    'ViewDateTime': +new Date()
  });
```

### 数据类型说明

* **object:** 上面 properties 是 object 类型，但是里面必须是 key: value 格式。