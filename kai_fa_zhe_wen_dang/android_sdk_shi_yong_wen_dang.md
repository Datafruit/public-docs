## Android SDK 使用文档 {#android-sdk}

### SDK 集成 {#sdk}

集成 Sugo Android SDK 有两种方式，**选择其中一种即可**。

**1.1 Gradle 集成** (暂未实现)

dependencies {

compile &#039;io.sugo.android:sugo-android-sdk:1.0.3&#039;

}

如果需要支持XWalkView，请使用

dependencies {

compile &#039;io.sugo.android:sugo-android-sdk-xwalk:1.0.3&#039;

}

**1.2 下载 SDK 并集成**下载 SDK 压缩包，解压后将其中的 sugo-android-sdk.jar 导入你的项目 libs 目录中

如果需要支持XWalkView，请导入sugo-android-sdk-xwalk.jar

**1.3 直接 clone 本**[**仓库**](https://github.com/Datafruit/sugo-android-sdk.git)**，作为 mobule 引用**

如果需要支持XwalkView，请 clone xwalk分支

### SDK 配置 {#sdk-0}

请先登录您的【数果智能】管理台，在数据管理-埋点项目-新建项目-新建应用中，创建您的应用，以获取对应的 Token 等。

#### AndroidManifest.xml 基本配置 {#androidmanifest-xml}
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"

>

_<!-- 必要的权限 -->_

<uses-permission android:name="android.permission.INTERNET"/>

<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>

<uses-permission android:name="android.permission.READ_PHONE_STATE"/>

<uses-permission android:name="android.permission.BLUETOOTH"/>

<application

...

>

...

<meta-data

android:name="io.sugo.android.SGConfig.EnableDebugLogging"

android:value="false"/>

<meta-data

android:name="io.sugo.android.SGConfig.token"

android:value="{YOUR_TOKEN}" />

<meta-data

android:name="io.sugo.android.SGConfig.ProjectId"

android:value="{YOUR_PROJECT}" />

</application>

</manifest>
```
*   SDK 配置说明

以下配置不包括{}，填入配置时，请删除大括号{}

权限说明

调试模式

开启调试模式，可以输出 SugoSDK 的日志
```
<meta-data

android:name="io.sugo.android.SGConfig.EnableDebugLogging"

android:value="true"/>
```
Token 设置

必填
```
<meta-data

android:name="io.sugo.android.SGConfig.token"

android:value="{YOUR_TOKEN}" />
```
Project Id 配置

必填
```
<meta-data

android:name="io.sugo.android.SGConfig.ProjectId"

android:value="{YOUR_PROJECT}" />
```
如果是私有云，请换成该配置
```
<meta-data

android:name="io.sugo.android.SGConfig.EventsEndpoint"

android:value="{EVENTS_ENDPOINT}?locate={YOUR_PROJECT}" />
```
部署地址配置

仅私有云配置
```
<meta-data

android:name="io.sugo.android.SGConfig.DecideEndpoint"

android:value="{DECIDE_ENDPOINT}" />
```
可视化埋点链接地址配置

仅私有云配置
```
<meta-data

android:name="io.sugo.android.SGConfig.EditorUrl"

android:value="{EDITOR_URL} />
```
代码混淆

如果您的应用使用了代码混淆， 请添加
```
-keepclassmembers **class** * {

public <init> (org.json.JSONObject);

}

-keepclassmembers **enum** * {

public static **[] values();

public static ** valueOf(java.lang.String);

}

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoAPI** { *; }

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoWebEventListener** { *; }

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoWebNodeReporter** { *; }
```
### SDK 使用 {#sdk-1}

标准的使用实例，应该是在 APP 启动的第一个Activity中，添加以下代码
```
**public** **class** **MainActivity** **extends** **Activity** {

SugoAPI mSugo;

**public** **void** **onCreate**(Bundle saved) {

_// 获取 SugoAPI 实例，在第一次调用时，SDK 将会初始化_

mSugo = SugoAPI.getInstance(**this**);

...

}

_// 使用 SugoAPI.track 方法来记录事件_

**public** **void** **whenSomethingInterestingHappens**(**int** flavor) {

JSONObject properties = **new** JSONObject();

properties.put("flavor", flavor);

mSugo.track("Something Interesting Happened", properties);

...

}

**public** **void** **onDestroy**() {

_// 如果启动的 Activity 不是最后销毁的 Activity，_

_// 这行代码可以移到对应的最后销毁的 Activity 的 onDestroy 方法里_

mSugo.flush(); _// 非必须，仅推荐使用_

**super**.onDestroy();

}

}
```
#### 基本功能

自定义事件

调用 SugoAPI.track()实现在代码中埋点来统计一次用户行为**参数说明**

*   eventId : 事件 id
*   eventName : 事件名称
*   properties : 事件的属性
```
SugoAPI sugoAPI = SugoAPI.getInstance(context);

JSONObject props = **new** JSONObject();

props.put("Gender", "Female");

props.put("Plan", "Premium");

sugoAPI.track("Plan Selected", props);
```
时长事件

调用SugoAPI.timeEvent()来追踪一个事件发生的时长，例如统计一次图片上传所消耗的时间
```
SugoAPI sugoAPI = SugoAPI.getInstance(context);

_// start the timer for the event "Image Upload"_

sugoAPI.timeEvent("Image Upload");

_// stop the timer if the imageUpload() method returns true_

**if**(imageUpload()){

sugoAPI.track("Image Upload");

}
```
开启 H5 埋点

**1.WebView 支持**
```
webView.getSettings().setJavaScriptEnabled(**true**);

SugoAPI instance = SugoAPI.getInstance(**this**);

instance.addWebViewJavascriptInterface(webView);

webView.setWebViewClient(**new** SugoWebViewClient() {

});

webView.loadUrl("http://sugo.io");
```
如果开发者不能实现或继承SugoWebViewClient，想要设置自己的WebViewClient，则需要在自己的WebViewClient的onPageFinished()回调里，调用SugoWebViewClient.handlePageFinished(webView, url)即可
```
webView.setWebViewClient(**new** WebViewClient() {

@Override

**public** **void** **onPageFinished**(WebView view, String url) {

**super**.onPageFinished(view, url);

SugoWebViewClient.handlePageFinished(webView, url);

}

});
```
**2.XWalkView 支持**

如果您的项目使用了 XWalkView 替代 WebView ，可以使用以下代码
```
XWalkView mXWalkView = (XWalkView) findViewById(R.id.xwalkview);

SugoAPI sugoAPI = SugoAPI.getInstance(**this**);

mXWalkView.setResourceClient(**new** XWalkResourceClient(mXWalkView) {

@Override

**public** **void** **onLoadFinished**(XWalkView view, String url) {

**super**.onLoadFinished(view, url);

SugoWebViewClient.handlePageFinished(view, url);

}

});

sugoAPI.addWebViewJavascriptInterface(mXWalkView);

mXWalkView.load("http://sugo.io", **null**);
```
**3.H5 代码埋点**

在进行了 3.1.3.1 或 3.1.3.2 的操作之后，SugoSDK 可支持 H5 的可视化埋点。如果需要代码埋点，可在 js 中调用以下方法：

自定义事件，参数如下：**参数说明**

*   event_id : 事件 id
*   event_name : 事件名称
*   props : 事件的属性
```
sugo.track(event_id, event_name, props);
```
时长事件
```
sugo.timeEvent(event_name);
```
默认属性

有一种很常见的情况，你想让一些常见的属性在每一个事件中都添加上，因此，你可以将这些属性注册为默认属性。

默认属性是保存在设备上的，将会持续存在 APP 里。SugoSDK 也设置了一些默认的默认属性，[点这里查看](http://note.youdao.com/md/preview/preview.html?file=%2Fyws%2Fapi%2Fpersonal%2Ffile%2FCC37957148D740E9B8FA46541EB72375%0A%20%20%20%20%20%20%3Fmethod%3Ddownload%26read%3Dtrue%26shareKey%3Dc89f3b142006e023782479a9f0f751dc)

想要设置默认属性，调用SugoAPI.registerSuperProperties
```
SugoAPI sugoAPI = SugoAPI.getInstance(context, MIXPANEL_TOKEN);

_// Send a "User Type: Paid" property will be sent with all future track calls._

JSONObject props = **new** JSONObject();

props.put("User Type", "Paid");

sugoAPI.registerSuperProperties(props);
```
接下来，你记录的任何事件，这些默认属性都会被一起发送。默认属性是存储在设备上的，退出 APP 后下次启动，该默认属性依旧存在。

**只设置一次默认属性**通过SugoAPI.registerSuperProperties会强行覆盖已有的同名属性，如果有些属性你想只设置一次（例如第一次登录的时间），可以调用SugoAPI.registerSuperPropertiesOnce，这个方法只保存第一次设置时的值，后面对该属性的修改操作将无效。

**移除默认属性**通过SugoAPI.unregisterSuperProperty可移除已设置的默认属性