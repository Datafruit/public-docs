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