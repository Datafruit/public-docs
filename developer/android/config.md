# SDK 配置

> 请先登录您的【听云星盘】管理台，在数据管理-埋点项目-新建项目-新建应用中，创建您的应用，以获取对应的 Token 等。   

### 1 AndroidManifest.xml 基本配置   

```xml   

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    >

    <!-- 必要的权限 -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    <uses-permission android:name="android.permission.BLUETOOTH"/>

  <application
    >
    ...
    <meta-data
            android:name="io.sugo.android.SGConfig.EnableDebugLogging"
            android:value="false"/>

    <!-- 也可以在代码中设置 Token 和 ProjectId -->
    <meta-data
            android:name="io.sugo.android.SGConfig.token"
            android:value="{YOUR_TOKEN}" />

    <meta-data
            android:name="io.sugo.android.SGConfig.ProjectId"
            android:value="{YOUR_PROJECT}" />

  </application>
</manifest>
```

- SDK 配置说明

> 以下配置不包括`{}`，填入配置时，请删除大括号`{}`

#### 1.0 权限说明   

- INTERNET：上报数据需要网络   
- ACCESS_NETWORK_STATE：获取当前的网络状态   
- ACCESS_WIFI_STATE：获取当前的 WIFI 状态   
- READ_PHONE_STATE：获取设备的信息（比如设备ID，用于标识唯一用户）   
- BLUETOOTH：获取设备是否支持蓝牙等   

#### 1.1 调试模式（建议在 Release 版本中设为 false）
> 开启调试模式，可以输出 SugoSDK 的日志

```xml
<meta-data
        android:name="io.sugo.android.SGConfig.EnableDebugLogging"
        android:value="true"/>
```

#### 1.2 Token 设置（必填，也可以在代码中配置）

```xml
<meta-data
        android:name="io.sugo.android.SGConfig.token"
        android:value="{YOUR_TOKEN}" />
```

#### 1.3 Project Id 配置（必填，也可以在代码中配置）

```xml
<meta-data
        android:name="io.sugo.android.SGConfig.ProjectId"
        android:value="{YOUR_PROJECT}" />
```

#### 1.4 上传数据的周期（默认60秒发送一次）   

```xml   
<meta-data
        android:name="io.sugo.android.SGConfig.FlushInterval"
        android:value="{毫秒}" />
```   

#### 1.5 更新埋点配置的周期（默认5分钟更新一次）   

```xml   
<meta-data
        android:name="io.sugo.android.SGConfig.UpdateDecideInterval"
        android:value="{毫秒}" />
```   

#### 1.6 数据有效期（默认5天有效期，5天未上传将被删除）   

```xml   
<meta-data
        android:name="io.sugo.android.SGConfig.DataExpiration"
        android:value="{毫秒}" />
```   

#### 1.7 数据上传容量（默认40条，一旦收集的数据达到 40 条，即使上传周期未到，也会触发数据上传）   

```xml   
<meta-data
        android:name="io.sugo.android.SGConfig.BulkUploadLimit"
        android:value="{数量}" />
```   


### 2 代码混淆
> 如果您的应用使用了代码混淆， 请添加


```

#保护注解
-keepattributes *Annotation*
#不混淆资源类
-keepclassmembers class **.R$* {
    public static <fields>;
}
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}
-keepattributes *JavascriptInterface*

-keep class io.sugo.* { *; }
-keep class io.sugo.android.mpmetrics.SugoAPI { *; }
-keep class io.sugo.android.mpmetrics.SugoWebEventListener { *; }
-keep class io.sugo.android.mpmetrics.SugoWebNodeReporter { *; }

```
