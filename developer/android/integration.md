# SDK 集成
> 集成 Sugo Android SDK 有两种方式，**选择其中一种即可**。

**1 Gradle 集成**
```Groovy
dependencies {
    compile 'io.sugo.android:sugo-android-sdk:2.0.0'
}
```

> 如果需要支持`XWalkView`，请另外再添加

```Groovy
dependencies {
    compile 'io.sugo.android:sugo-xwalkview-support:2.0.0'
}
```

**2 下载 SDK 并集成**
下载 SDK 压缩包，解压后将其中的 sugo-android-sdk.jar 导入你的项目 libs 目录中
> 如果需要支持`XWalkView`，请另外再导入`sugo-android-sdk-xwalk.jar`

---   
