#### 配置并获取SDK对象 {#sdk}

**1.添加头文件**

在集成了SDK的项目中，打开AppDelegate.m，在文件头部添加：
```
@import Sugo;
```
**2.添加SDK对象初始化代码**

把以下代码复制到AppDelegate.m中，并填入已获得的项目ID与Token：
```
(void)initSugo {

NSString *projectID = @&quot;Add_Your_Project_ID_Here&quot;;

NSString *appToken = @&quot;Add_Your_App_Token_Here&quot;;

[Sugo sharedInstanceWithID:projectID token:appToken launchOptions:nil];

[[Sugo sharedInstance] setEnableLogging:YES]; // 如果需要查看SDK的Log，请设置为true

[[Sugo sharedInstance] setFlushInterval:5]; // 被绑定的事件数据往服务端上传的事件间隔，单位是秒，如若不设置，默认时间是60秒

[[Sugo sharedInstance] identify:[Sugo sharedInstance].distinctId];

}
```
**3.调用SDK对象初始化代码**

添加initSugo后，在AppDelegate方法中调用，如下：
```
(BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

// Override point for customization after application launch.

[self initSugo]; //调用 `initSugo`

return YES;

}
```