# init参数配置

sugoio.init\('YOUR\_TOKEN', { // 项目TOKEN

project\_id: 'YOUR\_PROJECT\_ID', // 项目ID

api\_host: '', // sugoio-latest.min.js文件以及数据上报的地址

app\_host: '', // 可视化配置时服务端地址

decide\_host: '', // 加载已埋点配置地址

loaded: function\(**lib**\) { }, // **sugoio** **sdk** 加载完成回调函数

dimensions: { }, // 上报维度自定义映射配置参数

DEBUG: false // 是否启用debug

}\);

* **YOUR\_TOKEN：** 为项目TOKEN。
* **YOUR\_PROJECT\_ID：** 为项目ID。
* **api\_host：** sugoio-latest.min.js文件以及数据上报的地址。
* **app\_host：** 可视化配置时服务端地址。
* **decide\_host：** 加载已埋点配置地址。
* **loaded：** sugoio sdk 加载完成回调函数。
* **dimensions：** 上报维度自定义映射配置参数， 下文详细说明。
* **DEBUG：** 是否启用debug。

## 用户自定义维度

数果智能 的数据分析工具本身提供了例如 “访问来源”，“城市”,“操作系统"，”浏览器“等等这些维度。这些维度都可以和用户创建的指标进行多维的分析。但是往往不能满足用户对数据多维度分析的要求，因为每个公司的产品都有各自的用户维度，比如客户所服务的公司，用户正在使用的产品版本等等。 数果智能 为了能够让数据分析变得更加的灵活，我们在 JS SDK 中提供了用户自定义维度的API接口:

### **自定义事件上报\(代码埋点上报\)**

第一次接入 数果智能时，建议先追踪 3~5 个关键的事件，只需要几行代码，便能体验 数果智能 的分析功能。例如：

* 图片社交产品，可以追踪用户浏览图片和评论事件。
* 电商产品，可以追踪用户注册、浏览商品和下订单等事件。

数果智能 SDK 初始化成功后，即可以通过 sugoio.track\(event\_name, \[properties\], \[callback\]\) 记录事件：

* **event\_name**: string，必选。表示要追踪的事件名。
* **properties**: object，可选。表示这个事件的属性。
* **callback**: function，可选。表示已经发送完数据之后的回调。

_// 追踪浏览商品事件_

sugoio.track\('ViewProduct', {

'ProductId': 123456，

'ProductCatalog': "Laptop Computer",

'ProductName': 'MacBook Pro',

'ProductPrice': 888.88,

'ViewDateTime': +**new** Date\(\)

}\);

### 数据类型说明

* **object:** 上面 properties 是 object 类型，但是里面必须是 key: value 格式。



