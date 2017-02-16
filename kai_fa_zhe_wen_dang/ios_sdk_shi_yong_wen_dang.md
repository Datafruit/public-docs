## IOS SDK 使用文档 {#ios-sdk}

欢迎集成使用由sugo.io提供的iOS端Objective-C版采集并分析用户行为。

sugo-objc-sdk是一个开源项目，我们很期待能收到各界的代码贡献。

### 集成

#### CocoaPods {#cocoapods}

**现时我们的发布版本只能通过Cocoapods 1.1.0及以上的版本进行集成**

通过[CocoaPods](https://cocoapods.org/)，可方便地在项目中集成此SDK。

**1.配置Podfile**

请在项目根目录下的Podfile （如无，请创建或从我们提供的SugoDemo目录中[获取](https://github.com/Datafruit/sugo-objc-sdk/blob/master/SugoDemo/Podfile)并作出相应修改）文件中添加以下字符串：

pod &#039;sugo-objc-sdk&#039;

**2.执行集成命令**

关闭Xcode，并在Podfile目录下执行以下命令：

pod install

**3.完成**

运行完毕后，打开集成后的xcworkspace文件即可。

#### 手动安装 {#-1}

为了帮助开发者集成最新且稳定的SDK，我们建议通过Cocoapods来集成，这不仅简单而且易于管理。 然而，为了方便其他集成状况，我们也提供手动安装此SDK的方法。

**1.以子模块的形式添加**

以子模块的形式把sugo-objc-sdk添加进本地仓库中:

git submodule add git@github.com:Datafruit/sugo-objc-sdk.git

现在在仓库中能看见Sugo项目文件Sugo.xcodeproj了。

**2.把Sugo.xcodeproj拖到你的项目（或工作空间）中**

把Sugo.xcodeproj拖到需要被集成使用的项目文件中。

**3.嵌入框架（Embed the framework）**

选择需要被集成此SDK的项目target，把Sugo.framework以embeded binary形式添加进去。

### SDK的基础调用 {#sdk}

#### 获取SDK配置信息 {#sdk-1}

登陆数果智能后，可在平台界面中创建项目和数据接入方式，创建数据接入方式时，即可获得项目ID与Token。

#### 配置并获取SDK对象 {#sdk-2}

**1.添加头文件**

在集成了SDK的项目中，打开AppDelegate.m，在文件头部添加：

@import Sugo;

**2.添加SDK对象初始化代码**

把以下代码复制到AppDelegate.m中，并填入已获得的项目ID与Token：

- (void)initSugo {

NSString *projectID = @&quot;Add_Your_Project_ID_Here&quot;;

NSString *appToken = @&quot;Add_Your_App_Token_Here&quot;;

[Sugo sharedInstanceWithID:projectID token:appToken launchOptions:nil];

[[Sugo sharedInstance] setEnableLogging:YES]; // 如果需要查看SDK的Log，请设置为true

[[Sugo sharedInstance] setFlushInterval:5]; // 被绑定的事件数据往服务端上传的事件间隔，单位是秒，如若不设置，默认时间是60秒

[[Sugo sharedInstance] identify:[Sugo sharedInstance].distinctId];

}

**3.调用SDK对象初始化代码**

添加initSugo后，在AppDelegate方法中调用，如下：

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

// Override point for customization after application launch.

[self initSugo]; //调用 `initSugo`

return YES;

}

#### 扫码进入可视化埋点模式 {#-2}

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

#### 绑定事件 {#-3}

**1.原生控件**

**1.1 UIControl**

所有UIControl类及其子类，皆可被埋点绑定事件。

**1.2 UITableView**

所有UITableView类及其子类，需要指定其delegate属性，方可被埋点绑定事件。基于UITableView运行原理的特殊性，埋点绑定事件的时候只需要整个圈选，SDK会自动上报UITableView被选中的详细位置信息。

**2.UIWebView**

所有UIWebView类及其子类下的网页元素，需要指定其delegate属性，且在delegate指定类中实现以下指定的方法，方可被埋点绑定事件。

*   - (void)webViewDidStartLoad:(UIWebView *)webView;
*   - (void)webViewDidFinishLoad:(UIWebView *)webView;

**3.WKWebView**

所有WKWebView类及其子类下的网页元素，皆可被埋点绑定事件。

### SDK的进阶调用 {#sdk-0}

#### 获取全局对象 {#-4}

通过单例模式获取全局可用的对象，如下：

Sugo *sugo = [Sugo sharedInstance];

#### 手动埋点 {#-5}

**1.代码埋点**

当需要把自定义事件发送到服务器时，可在相应位置调用

*   - (void)track:(nullable NSString *)eventID eventName:(NSString *)eventName;

示例如下：

[[Sugo sharedInstance] track:@&quot;EventId&quot; eventName:@&quot;EventName&quot;]; //eventId可为空

当需要额外添加自定义的属性是，可调用

*   - (void)track:(NSString *)eventID eventName:(NSString *)eventName properties:(NSDictionary *)properties

示例如下：

[[Sugo sharedInstance] track:@&quot;EventId&quot; eventName:@&quot;EventName&quot; properties:@{ @&quot;key1&quot;: @&quot;value2&quot;, @&quot;key2&quot;: @&quot;value2&quot;}]; //eventId可为空

**2.时长统计**

**2.1创建**

当需要对时间进行跟踪统计时，可在开始跟踪的位置调用

*   - (void)timeEvent:(NSString *)event

示例如下：

[[Sugo sharedInstance] timeEvent:@&quot;TimeEventName&quot;];

**2.2发送**

然后，在完成跟踪的位置调用

- (void)track:(nullable NSString *)eventID eventName:(NSString *)eventName;

或

- (void)track:(NSString *)eventID eventName:(NSString *)eventName properties:(NSDictionary *)properties

即可，需要注意的是eventName需要与开始时的一样，如下：

[[Sugo sharedInstance] track:nil eventName:@&quot;TimeEventName&quot;]; //eventId可为空

**2.3更新**

如果在创建时间跟踪后，在发送前想要更新，再次以相同的时间事件名调用创建时的API即可，SDK会把相同事件名的记录时间进行刷新。

**2.4删除**

如果希望清除所有时间跟踪事件，可以通过调用

*   - (void)clearTimedEvents;

示例如下：

[[Sugo sharedInstance] clearTimedEvents];

**3.全局属性**

**3.1注册**

当每一个事件都需要记录相同的属性时，可以选择使用全局属性(全局属性仅允许NSString类型作为key值，value值则允许NSString、NSNumber、NSArray、NSDictionary、NSDate、NSURL这些类型)， 通过调用

*   - (void)registerSuperPropertiesOnce:(NSDictionary *)properties;
*   - (void)registerSuperProperties:(NSDictionary *)properties;

示例如下：

[[Sugo sharedInstance] registerSuperPropertiesOnce:@{ @&quot;key1&quot;: @&quot;value2&quot;, @&quot;key2&quot;: @&quot;value2&quot;}]; // 此方法不会覆盖当前已有的全局属性

[[Sugo sharedInstance] registerSuperProperties:@{ @&quot;key1&quot;: @&quot;value2&quot;, @&quot;key2&quot;: @&quot;value2&quot;}]; // 此方法会覆盖当前已有的全局属性

**3.2获取**

当需要获取已注册的全局属性时，可以调用

*   - (NSDictionary *)currentSuperProperties;

示例如下：

[[Sugo sharedInstance] currentSuperProperties];

**3.3注销**

当需要注销某一全局属性时，可以调用

*   - (void)unregisterSuperProperty:(NSString *)propertyName;

示例如下：

[[Sugo sharedInstance] unregisterSuperProperty:@&quot;SuperPropertyName&quot;];

**3.4清除**

当需要清除所有全局属性时，可以调用

*   - (void)clearSuperProperties;

示例如下：

[[Sugo sharedInstance] clearSuperProperties];

**4.WebView埋点**

当需要在WebView(UIWebView或WKWebView)中进行代码埋点时，在页面加载完毕后，可调用以下API(是3.2.1与3.2.2同名方法在JavaScript中的接口，实现机制相同)进行JavaScript内容的代码埋点

sugo.timeEvent(event_name); // 在开始统计时长的时候调用

sugo.track(event_id, event_name, props); // 准备把自定义事件发送到服务器时

### 反馈 {#-0}

已经成功集成了此SDK了，想了解SDK的最新动态, 请Star 或 Watch 我们的仓库： [Github](https://github.com/Datafruit/sugo-objc-sdk.git)。

有问题解决不了? 发送邮件到 [developer@sugo.io](https://github.com/Datafruit/sugo-objc-sdk/blob/master/Documentation/zh_Hans/developer@sugo.io) 或提出详细的issue，我们的进步，离不开各界的反馈。