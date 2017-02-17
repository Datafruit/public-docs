# SDK 集成

集成 Sugo Android SDK 有两种方式，**选择其中一种即可**。

## **1.1 Gradle 集成** \(暂未实现\)

dependencies {

compile 'io.sugo.android:sugo-android-sdk:1.0.3'

}

如果需要支持XWalkView，请使用

dependencies {

compile 'io.sugo.android:sugo-android-sdk-xwalk:1.0.3'

}

## **1.2 下载 SDK 并集成**

下载 SDK 压缩包，解压后将其中的 sugo-android-sdk.jar 导入你的项目 libs 目录中

如果需要支持XWalkView，请导入sugo-android-sdk-xwalk.jar

## **1.3 直接 clone 本**[**仓库**](https://github.com/Datafruit/sugo-android-sdk.git)**，作为 mobule 引用**

如果需要支持XwalkView，请 clone xwalk分支

