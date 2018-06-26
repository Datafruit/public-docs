## 2. SDK的基础调用

### 2.1 获取SDK配置信息

登陆听云星盘后，可在平台界面中创建项目和数据接入方式，创建数据接入方式时，即可获得项目ID与Token。

### 2.2 配置并获取SDK对象

#### 2.2.1 添加头文件

在集成了SDK的项目中，打开`AppDelegate.swift`，在文件头部添加：

```
import Sugo
```

#### 2.2.2 添加SDK对象初始化代码

把以下代码复制到`AppDelegate.swift`中，并填入已获得的项目ID与Token：

```
func initSugo() {
    let id: String = "Add_Your_Project_ID_Here"
    let token: String = "Add_Your_App_Token_Here"
    Sugo.initialize(id: id, token: token)
    Sugo.mainInstance().loggingEnabled = true   // 如果需要查看SDK的Log，请设置为true
    Sugo.mainInstance().flushInterval = 5       // 被绑定的事件数据往服务端上传的时间间隔，单位是秒，如若不设置，默认时间是60秒
    Sugo.mainInstance().cacheInterval = 60      // 从服务端拉取绑定事件配置的时间间隔，单位是秒，如若不设置，默认时间是1小时
    // Sugo.mainInstance().registerModule()     // 需要支持Weex可视化埋点时调用
}
```
#### 2.2.3 调用SDK对象初始化代码

添加`initSugo`后，在`AppDelegate`方法中调用，如下：

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    // Override point for customization after application launch.
    initSugo()
    return true
}
```

### 2.3 扫码进入可视化埋点模式

#### 2.3.1 通过扫码App进行扫码

##### 2.3.1.1 配置App的URL Types

在Xcode中，点击App的`xcodeproj`文件，进入`info`便签页，添加`URL Types`。

* Identifier: Sugo
* URL Schemes: sugo.\*  (“*”位置替换成Token)
* Icon: (可随意)
* Role: Editor

##### 2.3.1.2 选择被调用的API

`UIApplicationDelegate`中有3个可通过URL打开应用的方法，如下：

* `optional public func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool`
* `optional public func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool`
* `optional public func application(_ application: UIApplication, handleOpen url: URL) -> Bool`

请根据应用需适配的版本在`AppDelegate.swift`中实现其中一个或多个方法。

##### 2.3.1.3 可视化埋点模式API

在选定的方法中调用如下：

```
Sugo.mainInstance().handle(url: url)
```

##### 2.3.1.4 连接

登陆听云星盘，进入对应Token的可视化埋点界面，可看见二维码，保持埋点设备网络畅通，通过设备任意可扫二维码的应用扫一扫，然后用Safari打开链接，点击网页中的链接，即可进入可视化埋点模式。
此时设备上方将出现可视化埋点连接条，网页可视化埋点界面将显示设备当前页面及相应可绑定控件信息。

#### 2.3.2 通过自身应用进行扫码

若集成SDK的应用已把扫码功能开发完毕，也可通过自身的扫码功能进行可视化埋点模式的连接。即在扫码后，将已获取的URL作为参数，调用以下的方法：

* `open func connectToCodeless(via url: URL)`

该方法会对应用Token进行检验，若Token与初始化的值匹配，且已打开可视化埋点网页，则设备上方将出现可视化埋点连接条，网页可视化埋点界面将显示设备当前页面及相应可绑定控件信息，示例如下：

```
Sugo.mainInstance().connectToCodeless(via: url)    // url参数为扫描二维码后获得的值
```

### 2.4 绑定事件

#### 2.4.1 原生控件

**对于所有`UIView`，都有一个`String?`类型的`sugoViewId`属性，可以用于唯一指定容易混淆的可视化埋点视图，推荐初始化时设置使用**

可以通过如下方式设置：

```
view.sugoViewId = "CustomStringValue"
```

##### UIView

满足以下条件的`UIView`及其子类可以被可视化埋点绑定事件：

* `userInteractionEnabled`属性为`true`，且是`UIControl`或其子类
* `userInteractionEnabled`属性为`true`，且`gestureRecognizers`数组属性中包含`UITapGestureRecognizer`或其子类的手势实例，且其`enabled`属性为`true`

##### UITableView

所有`UITableView`类及其子类，需要指定其`delegate`属性，并实现以下方法，方可被埋点绑定事件。基于`UITableView`运行原理的特殊性，埋点绑定事件的时候只需要整个圈选，SDK会自动上报`UITableView`被选中的详细位置信息。

```
optional func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)
```

##### UICollectionView

所有`UICollectionView`类及其子类，需要指定其`delegate`属性，并实现以下方法，方可被埋点绑定事件。基于`UICollectionView`运行原理的特殊性，埋点绑定事件的时候只需要整个圈选，SDK会自动上报`UICollectionView`被选中的详细位置信息。

```
optional func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath)
```

#### 2.4.2 UIWebView

所有`UIWebView`类及其子类下的网页元素，需要指定其`delegate`属性，且在`delegate`指定类中实现以下指定的方法：

* `optional func webView(_ webView: UIWebView, shouldStartLoadWith request: URLRequest, navigationType: UIWebViewNavigationType) -> Bool`
* `optional public func webViewDidStartLoad(_ webView: UIWebView)`
* `optional public func webViewDidFinishLoad(_ webView: UIWebView)`

#### 2.4.3 WKWebView

所有`WKWebView`类及其子类下的网页元素，皆可被埋点绑定事件。