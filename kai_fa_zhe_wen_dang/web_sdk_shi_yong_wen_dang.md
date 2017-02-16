## WEB SDK 使用文档 {#web-sdk}

数果智能 提供 JS SDK,将代码插入到客户的网页中以后我们就可以接收用户的行为数据了。

我们的 JS SDK 支持 IE9 以上的 IE 浏览器、360 浏览器、谷歌浏览器、搜狗浏览器、火狐浏览器、QQ 浏览器、Safari 浏览器、Maxthon、Mobile 端浏览器，并且全面支持 H5，覆盖市面上主流的浏览器。

### 获取JS SDK {#js-sdk}

1.登录 数果智能 2.点击项目管理菜单下的“数据接入” 3.点击项目列表的“数据接入”按钮 (如果未创建项目，先点击“新建项目”) ![image](../assets/image.png)4.点击主分析表下的 “添加数据接入方式”

5.选择平台「网站」

![image](../assets/image.png)

6.安装SDK

![image](../assets/image.png)

### 整合JS SDK至web 环境 {#js-sdk-web}

将我们提供给您的 JS SDK 加入到您所需要分析的页面，请将我们给您提供的 JS SDK 复制到 &lt;head&gt; 和 &lt;/head&gt; 标签之间即可, 请务必更改“YOUR_TOKEN” , “YOUR_PROJECT_ID” 参数。您可以在数果智能 Web应用程序的项目管理的具体项目下的编辑界面的“查看设置”看到对应的TOKEN和PROJECT_ID。例如： ![image](../assets/image.png)

### 异步载入

&lt;head&gt;

...

_&lt;!-- start sugoio --&gt;_

&lt;script type=&#039;text/javascript&#039;&gt;

(**function**(e,a){**if**(!a.__SV){**var** b=window;**try**{**var** c,n,k,l=b.location,g=l.hash;c=**function**(a,b){**return**(n=a.match(**new** RegExp(b+&quot;=([^&amp;]*)&quot;)))?n[1]:null};g&amp;&amp;c(g,&quot;state&quot;)&amp;&amp;(k=JSON.parse(decodeURIComponent(c(g,&quot;state&quot;))),&quot;mpeditor&quot;===k.action&amp;&amp;(b.sessionStorage.setItem(&quot;_mpcehash&quot;,g),history.replaceState(k.desiredHash||&quot;&quot;,e.title,l.pathname+l.search)))}**catch**(p){}**var** m,h;window.sugoio=a;a._i=[];a.init=**function**(b,c,f){**function** **e**(b,a){**var** c=a.split(&quot;.&quot;);2==c.length&amp;&amp;(b=b[c[0]],a=c[1]);b[a]=**function**(){b.push([a].concat(Array.prototype.slice.call(arguments,

0)))}}**var** d=a;&quot;undefined&quot;!==**typeof** f?d=a[f]=[]:f=&quot;sugoio&quot;;d.people=d.people||[];d.toString=**function**(b){**var** a=&quot;sugoio&quot;;&quot;sugoio&quot;!==f&amp;&amp;(a+=&quot;.&quot;+f);b||(a+=&quot; (stub)&quot;);**return** a};d.people.toString=**function**(){**return** d.toString(1)+&quot;.people (stub)&quot;};m=&quot;disable time_event track track_pageview track_links track_forms register register_once alias unregister identify name_tag set_config reset people.set people.set_once people.increment people.append people.union people.track_charge people.clear_charges people.delete_user&quot;.split(&quot; &quot;);

**for**(h=0;h&lt;m.length;h++)e(d,m[h]);a._i.push([b,c,f])};a.__SV=1.2;b=e.createElement(&quot;script&quot;);b.type=&quot;text/javascript&quot;;b.async=!0;&quot;undefined&quot;!==**typeof** SUGOIO_CUSTOM_LIB_URL?b.src=SUGOIO_CUSTOM_LIB_URL:b.src=&quot;file:&quot;===e.location.protocol&amp;&amp;&quot;//localhost:8080/_bc/sugo-sdk-js/libs/sugoio-latest.min.js&quot;.match(/^\/\//)?&quot;https://localhost:8080/_bc/sugo-sdk-js/libs/sugoio-latest.min.js&quot;:&quot;//localhost:8080/_bc/sugo-sdk-js/libs/sugoio-latest.min.js&quot;;c=e.getElementsByTagName(&quot;script&quot;)[0];c.parentNode.insertBefore(b,

c)}})(document,window.sugoio||[]);

sugoio.init(&#039;YOUR_TOKEN&#039;, {&#039;project_id&#039;: &#039;YOUR_PROJECT_ID&#039;});

&lt;/script&gt;

_&lt;!-- end sugoio --&gt;_

...

&lt;/head&gt;

以上片段将异步方式加载我们库到页面，并提供了一个全局变量名为sugoio，你可以用它来代码埋点上报数据。 成功加载SDK后数果智能会自动采集页面浏览量。我们还提供一些高级设置：用户属性、页面属性、代码注入等。具体设置方法请参考下面的详细说明。

### init参数配置 {#init}

sugoio.init(&#039;YOUR_TOKEN&#039;, { // 项目TOKEN

project_id: &#039;YOUR_PROJECT_ID&#039;, // 项目ID

api_host: &#039;&#039;, // sugoio-latest.min.js文件以及数据上报的地址

app_host: &#039;&#039;, // 可视化配置时服务端地址

decide_host: &#039;&#039;, // 加载已埋点配置地址

loaded: function(**lib**) { }, // **sugoio** **sdk** 加载完成回调函数

dimensions: { }, // 上报维度自定义映射配置参数

DEBUG: false // 是否启用debug

});

*   **YOUR_TOKEN：** 为项目TOKEN。
*   **YOUR_PROJECT_ID：** 为项目ID。
*   **api_host：** sugoio-latest.min.js文件以及数据上报的地址。
*   **app_host：** 可视化配置时服务端地址。
*   **decide_host：** 加载已埋点配置地址。
*   **loaded：** sugoio sdk 加载完成回调函数。
*   **dimensions：** 上报维度自定义映射配置参数， 下文详细说明。
*   **DEBUG：** 是否启用debug。

#### 用户自定义维度 {#-4}

数果智能 的数据分析工具本身提供了例如 “访问来源”，“城市”,“操作系统&quot;，”浏览器“等等这些维度。这些维度都可以和用户创建的指标进行多维的分析。但是往往不能满足用户对数据多维度分析的要求，因为每个公司的产品都有各自的用户维度，比如客户所服务的公司，用户正在使用的产品版本等等。 数果智能 为了能够让数据分析变得更加的灵活，我们在 JS SDK 中提供了用户自定义维度的API接口:

**自定义事件上报(代码埋点上报)**

第一次接入 数果智能时，建议先追踪 3~5 个关键的事件，只需要几行代码，便能体验 数果智能 的分析功能。例如：

*   图片社交产品，可以追踪用户浏览图片和评论事件。
*   电商产品，可以追踪用户注册、浏览商品和下订单等事件。

数果智能 SDK 初始化成功后，即可以通过 sugoio.track(event_name, [properties], [callback]) 记录事件：

*   **event_name**: string，必选。表示要追踪的事件名。
*   **properties**: object，可选。表示这个事件的属性。
*   **callback**: function，可选。表示已经发送完数据之后的回调。

_// 追踪浏览商品事件_

sugoio.track(&#039;ViewProduct&#039;, {

&#039;ProductId&#039;: 123456，

&#039;ProductCatalog&#039;: &quot;Laptop Computer&quot;,

&#039;ProductName&#039;: &#039;MacBook Pro&#039;,

&#039;ProductPrice&#039;: 888.88,

&#039;ViewDateTime&#039;: +**new** Date()

});

#### 数据类型说明 {#-5}

*   **object:** 上面 properties 是 object 类型，但是里面必须是 key: value 格式。

### 事件公共属性(超级属性) {#-0}

sugoio.register 提供全局设置为每条上报记录都设置共有属性，在 Cookie 中永久保存属性，永久有效，如果存在这个属性了则覆盖

sugoio.register({

Custom1: &#039;Custom1&#039;,

Custom2: &#039;Custom2&#039;

...

});

数据类型跟上面提到的一样

### 预置属性 {#-1}

[&#039;sugo_nation&#039;, &#039;国家&#039;], //用户所在国家(ip反解析)

[&#039;sugo_province&#039;, &#039;省份&#039;], //用户所在省份(ip反解析)

[&#039;sugo_city&#039;, &#039;城市&#039;], //用户所在城市(ip反解析)

[&#039;sugo_district&#039;, &#039;地区&#039;], //用户所在地区(ip反解析)

[&#039;sugo_area&#039;, &#039;区域&#039;], //用户所在区域(ip反解析)

[&#039;sugo_latitude&#039;, &#039;纬度&#039;], //纬度(ip反解析)

[&#039;sugo_longitude&#039;, &#039;经度&#039;], //经度(ip反解析)

[&#039;sugo_city_timezone&#039;, &#039;城市时区&#039;], //所在时区代表城市(ip反解析)

[&#039;sugo_timezone&#039;, &#039;时区&#039;], //所在时区(ip反解析)

[&#039;sugo_phone_code&#039;, &#039;国际区号&#039;], //国际区号(ip反解析)

[&#039;sugo_nation_code&#039;, &#039;国家代码&#039;], //国家代码(ip反解析)

[&#039;sugo_continent&#039;, &#039;所在大洲&#039;], //所在大洲(ip反解析)

[&#039;sugo_administrative&#039;, &#039;行政区划代码&#039;], //中国行政区划代码(ip反解析)

[&#039;sugo_operator&#039;, &#039;运营商&#039;], //用户所在运营商(ip反解析)

[&#039;sugo_ip&#039;, &#039;客户端IP&#039;], //客户端IP(nginx)

[&#039;sugo_http_forward&#039;, &#039;客户端真实IP&#039;], //客户端真实ip(nginx)

[&#039;sugo_http_refer&#039;, &#039;Referer&#039;], //Referer(nginx)

[&#039;sugo_user_agent&#039;, &#039;浏览器标识&#039;], //浏览器标识(nginx)

[&#039;brower, &#039;浏览器名称&#039;], //浏览器名称

[&#039;brower_version&#039;, &#039;浏览器版本&#039;], //浏览器版本

[&#039;sugo_args&#039;, &#039;请求参数&#039;], //请求参数(nginx)

[&#039;sugo_http_cookie&#039;, &#039;HttpCookie&#039;], //HttpCookie(nginx)

[&#039;app_name&#039;, &#039;系统名称&#039;], //系统或app的系统名称

[&#039;app_version&#039;, &#039;系统版本&#039;], //系统或app的系统版本

[&#039;app_build_number&#039;, &#039;android build number&#039;], //android build number

[&#039;session_id&#039;,&#039;会话ID&#039;], //会话id

[&#039;network&#039;, &#039;网络类型&#039;], //用户使用的网络

[&#039;device_id&#039;,&#039;设备ID&#039;], //浏览器cookies（首次访问时生成)/Android或ios device id

[&#039;bluetooth_version&#039;, &#039;蓝牙版本&#039;], //用户蓝牙版本

[&#039;has_bluetooth&#039;, &#039;蓝牙版本&#039;], //用户是否有蓝牙

[&#039;device_brand&#039;, &#039;品牌&#039;], //用户电脑、平板、或手机牌子

[&#039;device_model&#039;, &#039;品牌型号&#039;], //用户电脑、平板、或手机型号

[&#039;system_name&#039;, &#039;浏览器/android/ios&#039;], //用户系统、平板或手机OS

[&#039;system_version&#039;, &#039;操作系统版本&#039;], //用户平板或手机OS版本

[&#039;radio&#039;, &#039;通信协议&#039;], //通信协议

[&#039;carrier&#039;, &#039;运营商&#039;], //运营商

[&#039;screen_dpi&#039;, &#039;分辨率&#039;], //客户端分辨率

[&#039;screen_height&#039;, &#039;屏幕高度&#039;], //客户端屏幕高度

[&#039;screen_width&#039;, &#039;屏幕宽度&#039;], //客户端屏幕宽度

[&#039;event_time&#039;, &#039;客户端事件时间&#039;, 4], //客户端事件发生时间（unix毫秒数)

[&#039;current_url&#039;, &#039;当前请求地址&#039;], //客户端当前请求地址

[&#039;referrer&#039;, &#039;客户引荐&#039;], //客户引荐

[&#039;referring_domain&#039;, &#039;客户引荐域名&#039;], //客户引荐域名

[&#039;host&#039;, &#039;客户端域名&#039;], //客户端域名

[&#039;distinct_id&#039;, &#039;用户唯一ID&#039;], //用户唯一ID

[&#039;has_nfc&#039;, &#039;NFC功能&#039;], //NFC功能

[&#039;has_telephone&#039;, &#039;电话功能&#039;], //电话功能

[&#039;has_wifi&#039;, &#039;WIFI功能&#039;], //wifi功能

[&#039;manufacturer&#039;, &#039;设备制造商&#039;], //设备制造商

[&#039;duration&#039;, &#039;停留时间&#039;, 1], //页面停留时间

[&#039;sdk_version&#039;, &#039;SDK版本&#039;], //sdk版本

[&#039;page_name&#039;, &#039;页面名称&#039;], //页面名称或屏幕名称

[&#039;path_name&#039;, &#039;页面路径&#039;], //页面路径

[&#039;event_id&#039;, &#039;事件ID&#039;], //事件ID

[&#039;event_name&#039;, &#039;事件名称&#039;], //事件名称

[&#039;event_type&#039;, &#039;事件类型&#039;], //事件类型click、focus、submit、change

[&#039;event_label&#039;, &#039;事件源文本&#039;], //事件源文本

[&#039;sugo_lib&#039;, &#039;sdk类型&#039;], //sdk类型 web、ios、android

[&#039;token&#039;, &#039;应用ID&#039;], //应用ID

[&#039;from_binding&#039;, &#039;是否绑定事件&#039;], //是否绑定事件（用于区分是绑定的，还是系统自动上报的，比如浏览、启动为自动上报，绑定取值1，自动上报取值0）

[&#039;google_play_services&#039;, &#039;GooglePlay服务&#039;], //GooglePlay服务

### 维度映射 {#-2}

sugoio.init(&#039;YOUR_TOKEN&#039;, {

project_id: &#039;YOUR_PROJECT_ID&#039;,

dimensions: {

has_wifi: &#039;wifi&#039;,

sdk_version: &#039;appVersion&#039;

...

}

...

});

### 页面停留事件 {#-3}

sugoio.init(&#039;YOUR_TOKEN&#039;, {

project_id: &#039;YOUR_PROJECT_ID&#039;,

loaded: **function**(lib) {

sugoio.time_event(&#039;停留&#039;)

sugoio._.register_event(window, &#039;beforeunload&#039;, **function**(){

sugoio.track(&#039;停留&#039;, {path_name: location.pathname})

}, false, true)

}

....

});

#### sugoio.register_once(object) {#sugoio-register-once-object}

在 Cookie 中永久保存属性，如果存在这个属性了则不覆盖