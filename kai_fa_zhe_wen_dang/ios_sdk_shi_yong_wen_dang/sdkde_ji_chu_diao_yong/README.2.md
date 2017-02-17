# SDK的基础调用

## 获取SDK配置信息

登陆数果智能后，可在平台界面中创建项目和数据接入方式，创建数据接入方式时，即可获得项目ID与Token。

## 配置并获取SDK对象

### **1.添加头文件**

在集成了SDK的项目中，打开AppDelegate.m，在文件头部添加：

@import Sugo;

### **2.添加SDK对象初始化代码**

把以下代码复制到AppDelegate.m中，并填入已获得的项目ID与Token：

* \(void\)initSugo {

NSString \*projectID = @"Add\_Your\_Project\_ID\_Here";

NSString \*appToken = @"Add\_Your\_App\_Token\_Here";

\[Sugo sharedInstanceWithID:projectID token:appToken launchOptions:nil\];

\[\[Sugo sharedInstance\] setEnableLogging:YES\]; // 如果需要查看SDK的Log，请设置为true

\[\[Sugo sharedInstance\] setFlushInterval:5\]; // 被绑定的事件数据往服务端上传的事件间隔，单位是秒，如若不设置，默认时间是60秒

\[\[Sugo sharedInstance\] identify:\[Sugo sharedInstance\].distinctId\];

}

### **3.调用SDK对象初始化代码**

添加initSugo后，在AppDelegate方法中调用，如下：

* \(BOOL\)application:\(UIApplication _\)application didFinishLaunchingWithOptions:\(NSDictionary _\)launchOptions {

// Override point for customization after application launch.

\[self initSugo\]; //调用 `initSugo`

return YES;

}

## 扫码进入可视化埋点模式

### **1.配置App的URL Types**

在Xcode中，点击App的xcodeproj文件，进入info便签页，添加URL Types。

* Identifier: Sugo
* URL Schemes: sugo._ \(“_”位置替换成Token\)
* Icon: \(可随意\)
* Role: Editor

### **2.选择被调用的API**

UIApplicationDelegate中有3个可通过URL打开应用的方法，如下：

* * \(BOOL\)application:\(UIApplication _\)app openURL:\(NSURL _\)url options:\(NSDictionary&lt;UIApplicationOpenURLOptionsKey,id&gt; \*\)options;
* * \(BOOL\)application:\(UIApplication _\)application openURL:\(NSURL _\)url sourceApplication:\(NSString \*\)sourceApplication annotation:\(id\)annotation;
* * \(BOOL\)application:\(UIApplication _\)application handleOpenURL:\(NSURL _\)url;

请根据应用需适配的版本在AppDelegate.m中实现其中一个或多个方法。

### **3.可视化埋点模式API**

在选定的方法中调用如下：

\[\[Sugo sharedInstance\] handleURL:url\]; //返回值为BOOL类型，可作为方法的返回值。

### **4.连接**

登陆数果智能，进入对应Token的可视化埋点界面，可看见二维码，保持埋点设备网络畅通，通过设备任意可扫二维码的应用扫一扫，然后用Safari打开链接，点击网页中的链接，即可进入可视化埋点模式。 此时设备上方将出现可视化埋点连接条，网页可视化埋点界面将显示设备当前页面及相应可绑定控件信息。

## 绑定事件

### **1.原生控件**

#### **1.1 UIControl**

所有UIControl类及其子类，皆可被埋点绑定事件。

#### **1.2 UITableView**

所有UITableView类及其子类，需要指定其delegate属性，方可被埋点绑定事件。基于UITableView运行原理的特殊性，埋点绑定事件的时候只需要整个圈选，SDK会自动上报UITableView被选中的详细位置信息。

#### **2.UIWebView**

所有UIWebView类及其子类下的网页元素，需要指定其delegate属性，且在delegate指定类中实现以下指定的方法，方可被埋点绑定事件。

* * \(void\)webViewDidStartLoad:\(UIWebView \*\)webView;
* * \(void\)webViewDidFinishLoad:\(UIWebView \*\)webView;

#### **3.WKWebView**

所有WKWebView类及其子类下的网页元素，皆可被埋点绑定事件。

