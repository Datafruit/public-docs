#### AndroidManifest.xml 基本配置 {#androidmanifest-xml}

&lt;manifest xmlns:android=&quot;http://schemas.android.com/apk/res/android&quot;

&gt;

_&lt;!-- 必要的权限 --&gt;_

&lt;uses-permission android:name=&quot;android.permission.INTERNET&quot;/&gt;

&lt;uses-permission android:name=&quot;android.permission.ACCESS_NETWORK_STATE&quot;/&gt;

&lt;uses-permission android:name=&quot;android.permission.ACCESS_WIFI_STATE&quot;/&gt;

&lt;uses-permission android:name=&quot;android.permission.READ_PHONE_STATE&quot;/&gt;

&lt;uses-permission android:name=&quot;android.permission.BLUETOOTH&quot;/&gt;

&lt;application

...

&gt;

...

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.EnableDebugLogging&quot;

android:value=&quot;false&quot;/&gt;

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.token&quot;

android:value=&quot;{YOUR_TOKEN}&quot; /&gt;

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.ProjectId&quot;

android:value=&quot;{YOUR_PROJECT}&quot; /&gt;

&lt;/application&gt;

&lt;/manifest&gt;

*   SDK 配置说明

以下配置不包括{}，填入配置时，请删除大括号{}

权限说明

调试模式

开启调试模式，可以输出 SugoSDK 的日志

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.EnableDebugLogging&quot;

android:value=&quot;true&quot;/&gt;

Token 设置

必填

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.token&quot;

android:value=&quot;{YOUR_TOKEN}&quot; /&gt;

Project Id 配置

必填

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.ProjectId&quot;

android:value=&quot;{YOUR_PROJECT}&quot; /&gt;

如果是私有云，请换成该配置

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.EventsEndpoint&quot;

android:value=&quot;{EVENTS_ENDPOINT}?locate={YOUR_PROJECT}&quot; /&gt;

部署地址配置

仅私有云配置

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.DecideEndpoint&quot;

android:value=&quot;{DECIDE_ENDPOINT}&quot; /&gt;

可视化埋点链接地址配置

仅私有云配置

&lt;meta-data

android:name=&quot;io.sugo.android.SGConfig.EditorUrl&quot;

android:value=&quot;{EDITOR_URL} /&gt;

代码混淆

如果您的应用使用了代码混淆， 请添加

-keepclassmembers **class** * {

public &lt;init&gt; (org.json.JSONObject);

}

-keepclassmembers **enum** * {

public static **[] values();

public static ** valueOf(java.lang.String);

}

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoAPI** { *; }

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoWebEventListener** { *; }

-keep **class** **io**.**sugo**.**android**.**mpmetrics**.**SugoWebNodeReporter** { *; }