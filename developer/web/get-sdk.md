# 获取JS SDK

1.登录 数果智能

2.点击项目管理菜单下的“数据接入”

3.点击项目列表的“数据接入”按钮 (如果未创建项目，先点击“新建项目”) 

 ![](/assets/web-maidian/1.png)
 
4.点击主分析表下的 “添加数据接入方式”

5.选择平台「网站」

![](/assets/web-maidian/2.png)

6.安装SDK

![](/assets/web-maidian/3.png)


# 整合JS SDK至web 环境

将我们提供给您的 JS SDK 加入到您所需要分析的页面，请将我们给您提供的 JS SDK 复制到 &lt;head&gt; 和 &lt;/head&gt; 标签之间即可, 请务必更改“YOUR_TOKEN” , “YOUR_PROJECT_ID” 参数。您可以在数果智能 Web应用程序的项目管理的具体项目下的编辑界面的“查看设置”看到对应的TOKEN和PROJECT_ID。例如：   
![](/assets/web-maidian/4.png)
# 异步载入
```
<head>
...
<!-- start sugoio -->
  <script type='text/javascript'>
  
    // var SUGOIO_CUSTOM_LIB_URL = "http://astro.sugo.io/_bc/sugo-sdk-js/libs/sugoio-latest.min.js";

    (function(e,a){if(!a.__SV){var b=window;try{var c,n,k,l=b.location,g=l.hash;c=function(a,b){return(n=a.match(new RegExp(b+"=([^&]*)")))?n[1]:null};g&&c(g,"state")&&(k=JSON.parse(decodeURIComponent(c(g,"state"))),"mpeditor"===k.action&&(b.sessionStorage.setItem("_mpcehash",g),history.replaceState(k.desiredHash||"",e.title,l.pathname+l.search)))}catch(p){}var m,h;window.sugoio=a;a._i=[];a.init=function(b,c,f){function e(b,a){var c=a.split(".");2==c.length&&(b=b[c[0]],a=c[1]);b[a]=function(){b.push([a].concat(Array.prototype.slice.call(arguments,
    0)))}}var d=a;"undefined"!==typeof f?d=a[f]=[]:f="sugoio";d.people=d.people||[];d.toString=function(b){var a="sugoio";"sugoio"!==f&&(a+="."+f);b||(a+=" (stub)");return a};d.people.toString=function(){return d.toString(1)+".people (stub)"};m="disable time_event track track_pageview track_links track_forms register register_once alias unregister identify name_tag set_config reset people.set people.set_once people.increment people.append people.union people.track_charge people.clear_charges people.delete_user".split(" ");
    for(h=0;h<m.length;h++)e(d,m[h]);a._i.push([b,c,f])};a.__SV=1.2;b=e.createElement("script");b.type="text/javascript";b.async=!0;"undefined"!==typeof SUGOIO_CUSTOM_LIB_URL?b.src=SUGOIO_CUSTOM_LIB_URL:b.src="file:"===e.location.protocol&&"//astro.sugo.io/_bc/sugo-sdk-js/libs/sugoio-latest.min.js".match(/^\/\//)?"https://astro.sugo.io/_bc/sugo-sdk-js/libs/sugoio-latest.min.js":"//astro.sugo.io/_bc/sugo-sdk-js/libs/sugoio-latest.min.js";c=e.getElementsByTagName("script")[0];c.parentNode.insertBefore(b,
    c)}})(document,window.sugoio||[]);
    
    sugoio.init('YOUR_TOKEN', {'project_id': 'YOUR_PROJECT_ID'});
  </script>
<!-- end sugoio -->
...
</head>
```

以上片段将异步方式加载我们库到页面，并提供了一个全局变量名为sugoio，你可以用它来代码埋点上报数据。 成功加载SDK后数果智能会自动采集页面浏览量。我们还提供一些高级设置：用户属性、页面属性、代码注入等。具体设置方法请参考下面的详细说明。

# init参数配置

```javascript
  sugoio.init('YOUR_TOKEN', { // 项目TOKEN
    project_id: 'YOUR_PROJECT_ID', // 项目ID
    api_host: '', // sugoio-latest.min.js文件以及数据上报的地址
    app_host: '', // 可视化配置时服务端地址
    decide_host: '', // 加载已埋点配置地址
    loaded: function(lib) { }, // **sugoio** **sdk** 加载完成回调函数
    dimensions: { }, // 上报维度自定义映射配置参数
    DEBUG: false // 是否启用debug
  });
```

* **YOUR_TOKEN：** 为项目TOKEN。
* **YOUR_PROJECT_ID：** 为项目ID。
* **api_host：** sugoio-latest.min.js文件以及数据上报的地址。
* **app_host：** 可视化配置时服务端地址。
* **decide_host：** 加载已埋点配置地址。
* **loaded：** sugoio sdk 加载完成回调函数。
* **dimensions：** 上报维度自定义映射配置参数， 下文详细说明。
* **DEBUG：** 是否启用debug。

## 用户自定义维度

数果智能 的数据分析工具本身提供了例如 “访问来源”，“城市”,“操作系统"，”浏览器“等等这些维度。这些维度都可以和用户创建的指标进行多维的分析。但是往往不能满足用户对数据多维度分析的要求，因为每个公司的产品都有各自的用户维度，比如客户所服务的公司，用户正在使用的产品版本等等。 数果智能 为了能够让数据分析变得更加的灵活，我们在 JS SDK 中提供了用户自定义维度的API接口:

### **自定义事件上报(代码埋点上报)**

第一次接入 数果智能时，建议先追踪 3~5 个关键的事件，只需要几行代码，便能体验 数果智能 的分析功能。例如：

* 图片社交产品，可以追踪用户浏览图片和评论事件。
* 电商产品，可以追踪用户注册、浏览商品和下订单等事件。

数果智能 SDK 初始化成功后，即可以通过 sugoio.track\(event\_name, \[properties\], \[callback\]\) 记录事件：

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


# 事件公共属性(超级属性)
```javascript
  // sugoio.register 提供全局设置为每条上报记录都设置共有属性，在 Cookie 中永久保存属性，永久有效，如果存在这个属性了则覆盖
  sugoio.register({
    Custom1: 'Custom1',
    Custom2: 'Custom2'
    ...
  });
```

# 页面停留事件
```javascript
  sugoio.init('YOUR_TOKEN', {
    project_id: 'YOUR_PROJECT_ID',
    loaded: function(lib) {
      sugoio.time_event('停留')
      sugoio._.register_event(window, 'beforeunload', function(){
      sugoio.track('停留', {path_name: location.pathname})
    }, false, true)
  }
  ....
  });
```

## sugoio.register_once(object)

在 Cookie 中永久保存属性，如果存在这个属性了则不覆盖
