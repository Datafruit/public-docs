# SDK 配置

请先登录您的【数果智能】管理台，在数据管理-埋点项目-新建项目-新建应用中，创建您的应用，以获取对应的 Token 等。
```javascript

## AndroidManifest.xml 基本配置

&lt;manifest xmlns:android="[http://schemas.android.com/apk/res/android"](http://schemas.android.com/apk/res/android&quot);

&gt;

_&lt;!-- 必要的权限 --&gt;_

&lt;uses-permission android:name="android.permission.INTERNET"/&gt;

&lt;uses-permission android:name="android.permission.ACCESS\_NETWORK\_STATE"/&gt;

&lt;uses-permission android:name="android.permission.ACCESS\_WIFI\_STATE"/&gt;

&lt;uses-permission android:name="android.permission.READ\_PHONE\_STATE"/&gt;

&lt;uses-permission android:name="android.permission.BLUETOOTH"/&gt;

&lt;application

...

&gt;

...

&lt;meta-data

android:name="io.sugo.android.SGConfig.EnableDebugLogging"

android:value="false"/&gt;

&lt;meta-data

android:name="io.sugo.android.SGConfig.token"

android:value="{YOUR\_TOKEN}" /&gt;

&lt;meta-data

android:name="io.sugo.android.SGConfig.ProjectId"

android:value="{YOUR\_PROJECT}" /&gt;

&lt;/application&gt;

&lt;/manifest&gt;

* SDK 配置说明

以下配置不包括{}，填入配置时，请删除大括号{}

权限说明

调试模式

开启调试模式，可以输出 SugoSDK 的日志

&lt;meta-data

android:name="io.sugo.android.SGConfig.EnableDebugLogging"

android:value="true"/&gt;

Token 设置

必填

&lt;meta-data

android:name="io.sugo.android.SGConfig.token"

android:value="{YOUR\_TOKEN}" /&gt;

Project Id 配置

必填

&lt;meta-data

android:name="io.sugo.android.SGConfig.ProjectId"

android:value="{YOUR\_PROJECT}" /&gt;

如果是私有云，请换成该配置

&lt;meta-data

android:name="io.sugo.android.SGConfig.EventsEndpoint"

android:value="{EVENTS\_ENDPOINT}?locate={YOUR\_PROJECT}" /&gt;

部署地址配置

仅私有云配置

&lt;meta-data

android:name="io.sugo.android.SGConfig.DecideEndpoint"

android:value="{DECIDE\_ENDPOINT}" /&gt;

可视化埋点链接地址配置

仅私有云配置

&lt;meta-data

android:name="io.sugo.android.SGConfig.EditorUrl"

android:value="{EDITOR\_URL} /&gt;

代码混淆

如果您的应用使用了代码混淆， 请添加

-keepclassmembers **class** \* {

public &lt;init&gt; \(org.json.JSONObject\);

}

-keepclassmembers **enum** \* {

public static \*\*\[\] values\(\);

public static \*\* valueOf\(java.lang.String\);

}

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoAPI** { \*; }

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoWebEventListener** { \*; }

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoWebNodeReporter** { \*; }

```