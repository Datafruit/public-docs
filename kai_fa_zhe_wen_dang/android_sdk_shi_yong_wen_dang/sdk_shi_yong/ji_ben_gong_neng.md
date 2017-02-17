# 基本功能

## 自定义事件

调用 SugoAPI.track\(\)实现在代码中埋点来统计一次用户行为**参数说明**

* eventId : 事件 id
* eventName : 事件名称
* properties : 事件的属性

SugoAPI sugoAPI = SugoAPI.getInstance\(context\);

JSONObject props = **new** JSONObject\(\);

props.put\("Gender", "Female"\);

props.put\("Plan", "Premium"\);

sugoAPI.track\("Plan Selected", props\);

## 时长事件

调用SugoAPI.timeEvent\(\)来追踪一个事件发生的时长，例如统计一次图片上传所消耗的时间

SugoAPI sugoAPI = SugoAPI.getInstance\(context\);

_// start the timer for the event "Image Upload"_

sugoAPI.timeEvent\("Image Upload"\);

_// stop the timer if the imageUpload\(\) method returns true_

**if**\(imageUpload\(\)\){

sugoAPI.track\("Image Upload"\);

}

## 开启 H5 埋点

### **1.WebView 支持**

webView.getSettings\(\).setJavaScriptEnabled\(**true**\);

SugoAPI instance = SugoAPI.getInstance\(**this**\);

instance.addWebViewJavascriptInterface\(webView\);

webView.setWebViewClient\(**new** SugoWebViewClient\(\) {

}\);

webView.loadUrl\("[http://sugo.io"](http://sugo.io&quot);\);

如果开发者不能实现或继承SugoWebViewClient，想要设置自己的WebViewClient，则需要在自己的WebViewClient的onPageFinished\(\)回调里，调用SugoWebViewClient.handlePageFinished\(webView, url\)即可

webView.setWebViewClient\(**new** WebViewClient\(\) {

@Override

**public** **void** **onPageFinished**\(WebView view, String url\) {

**super**.onPageFinished\(view, url\);

SugoWebViewClient.handlePageFinished\(webView, url\);

}

}\);

### **2.XWalkView 支持**

如果您的项目使用了 XWalkView 替代 WebView ，可以使用以下代码

XWalkView mXWalkView = \(XWalkView\) findViewById\(R.id.xwalkview\);

SugoAPI sugoAPI = SugoAPI.getInstance\(**this**\);

mXWalkView.setResourceClient\(**new** XWalkResourceClient\(mXWalkView\) {

@Override

**public** **void** **onLoadFinished**\(XWalkView view, String url\) {

**super**.onLoadFinished\(view, url\);

SugoWebViewClient.handlePageFinished\(view, url\);

}

}\);

sugoAPI.addWebViewJavascriptInterface\(mXWalkView\);

mXWalkView.load\("[http://sugo.io"](http://sugo.io&quot);, **null**\);

### **3.H5 代码埋点**

在进行了 3.1.3.1 或 3.1.3.2 的操作之后，SugoSDK 可支持 H5 的可视化埋点。如果需要代码埋点，可在 js 中调用以下方法：

自定义事件，参数如下：**参数说明**

* event\_id : 事件 id
* event\_name : 事件名称
* props : 事件的属性

sugo.track\(event\_id, event\_name, props\);

## 时长事件

sugo.timeEvent\(event\_name\);

## 默认属性

有一种很常见的情况，你想让一些常见的属性在每一个事件中都添加上，因此，你可以将这些属性注册为默认属性。

默认属性是保存在设备上的，将会持续存在 APP 里。SugoSDK 也设置了一些默认的默认属性，[点这里查看](http://note.youdao.com/md/preview/preview.html?file=%2Fyws%2Fapi%2Fpersonal%2Ffile%2FCC37957148D740E9B8FA46541EB72375%0A%20%20%20%20%20%20%3Fmethod%3Ddownload%26read%3Dtrue%26shareKey%3Dc89f3b142006e023782479a9f0f751dc)

想要设置默认属性，调用SugoAPI.registerSuperProperties

SugoAPI sugoAPI = SugoAPI.getInstance\(context, MIXPANEL\_TOKEN\);

_// Send a "User Type: Paid" property will be sent with all future track calls._

JSONObject props = **new** JSONObject\(\);

props.put\("User Type", "Paid"\);

sugoAPI.registerSuperProperties\(props\);

接下来，你记录的任何事件，这些默认属性都会被一起发送。默认属性是存储在设备上的，退出 APP 后下次启动，该默认属性依旧存在。

**只设置一次默认属性**通过SugoAPI.registerSuperProperties会强行覆盖已有的同名属性，如果有些属性你想只设置一次（例如第一次登录的时间），可以调用SugoAPI.registerSuperPropertiesOnce，这个方法只保存第一次设置时的值，后面对该属性的修改操作将无效。

**移除默认属性**通过SugoAPI.unregisterSuperProperty可移除已设置的默认属性

