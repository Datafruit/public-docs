#### 扫码进入可视化埋点模式

**1.配置App的URL Types**

在Xcode中，点击App的xcodeproj文件，进入info便签页，添加URL Types。

*   Identifier: Sugo
*   URL Schemes: sugo.* (“*”位置替换成Token)
*   Icon: (可随意)
*   Role: Editor

**2.选择被调用的API**

UIApplicationDelegate中有3个可通过URL打开应用的方法，如下：

*   - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary&lt;UIApplicationOpenURLOptionsKey,id&gt; *)options;
*   - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation;
*   - (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url;

请根据应用需适配的版本在AppDelegate.m中实现其中一个或多个方法。

**3.可视化埋点模式API**

在选定的方法中调用如下：

[[Sugo sharedInstance] handleURL:url]; //返回值为BOOL类型，可作为方法的返回值。

**4.连接**

登陆数果智能，进入对应Token的可视化埋点界面，可看见二维码，保持埋点设备网络畅通，通过设备任意可扫二维码的应用扫一扫，然后用Safari打开链接，点击网页中的链接，即可进入可视化埋点模式。 此时设备上方将出现可视化埋点连接条，网页可视化埋点界面将显示设备当前页面及相应可绑定控件信息。