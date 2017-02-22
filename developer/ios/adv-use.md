# SDK的进阶调用

## 获取全局对象

通过单例模式获取全局可用的对象，如下：

Sugo \*sugo = \[Sugo sharedInstance\];

## 手动埋点

### **1.代码埋点**

当需要把自定义事件发送到服务器时，可在相应位置调用

* * \(void\)track:\(nullable NSString _\)eventID eventName:\(NSString _\)eventName;

示例如下：

\[\[Sugo sharedInstance\] track:@"EventId" eventName:@"EventName"\]; //eventId可为空

当需要额外添加自定义的属性是，可调用

* * \(void\)track:\(NSString _\)eventID eventName:\(NSString _\)eventName properties:\(NSDictionary \*\)properties

示例如下：

\[\[Sugo sharedInstance\] track:@"EventId" eventName:@"EventName" properties:@{ @"key1": @"value2", @"key2": @"value2"}\]; //eventId可为空

### **2.时长统计**

#### **2.1创建**

当需要对时间进行跟踪统计时，可在开始跟踪的位置调用

* * \(void\)timeEvent:\(NSString \*\)event

示例如下：

\[\[Sugo sharedInstance\] timeEvent:@"TimeEventName"\];

#### **2.2发送**

然后，在完成跟踪的位置调用

* \(void\)track:\(nullable NSString _\)eventID eventName:\(NSString _\)eventName;

或

* \(void\)track:\(NSString _\)eventID eventName:\(NSString _\)eventName properties:\(NSDictionary \*\)properties

即可，需要注意的是eventName需要与开始时的一样，如下：

\[\[Sugo sharedInstance\] track:nil eventName:@"TimeEventName"\]; //eventId可为空

#### **2.3更新**

如果在创建时间跟踪后，在发送前想要更新，再次以相同的时间事件名调用创建时的API即可，SDK会把相同事件名的记录时间进行刷新。

#### **2.4删除**

如果希望清除所有时间跟踪事件，可以通过调用

* * \(void\)clearTimedEvents;

示例如下：

\[\[Sugo sharedInstance\] clearTimedEvents\];

### **3.全局属性**

#### **3.1注册**

当每一个事件都需要记录相同的属性时，可以选择使用全局属性\(全局属性仅允许NSString类型作为key值，value值则允许NSString、NSNumber、NSArray、NSDictionary、NSDate、NSURL这些类型\)， 通过调用

* * \(void\)registerSuperPropertiesOnce:\(NSDictionary \*\)properties;
* * \(void\)registerSuperProperties:\(NSDictionary \*\)properties;

示例如下：

\[\[Sugo sharedInstance\] registerSuperPropertiesOnce:@{ @"key1": @"value2", @"key2": @"value2"}\]; // 此方法不会覆盖当前已有的全局属性

\[\[Sugo sharedInstance\] registerSuperProperties:@{ @"key1": @"value2", @"key2": @"value2"}\]; // 此方法会覆盖当前已有的全局属性

#### **3.2获取**

当需要获取已注册的全局属性时，可以调用

* * \(NSDictionary \*\)currentSuperProperties;

示例如下：

\[\[Sugo sharedInstance\] currentSuperProperties\];

#### **3.3注销**

当需要注销某一全局属性时，可以调用

* * \(void\)unregisterSuperProperty:\(NSString \*\)propertyName;

示例如下：

\[\[Sugo sharedInstance\] unregisterSuperProperty:@"SuperPropertyName"\];

#### **3.4清除**

当需要清除所有全局属性时，可以调用

* * \(void\)clearSuperProperties;

示例如下：

\[\[Sugo sharedInstance\] clearSuperProperties\];

### **4.WebView埋点**

当需要在WebView\(UIWebView或WKWebView\)中进行代码埋点时，在页面加载完毕后，可调用以下API\(是3.2.1与3.2.2同名方法在JavaScript中的接口，实现机制相同\)进行JavaScript内容的代码埋点

sugo.timeEvent\(event\_name\); // 在开始统计时长的时候调用

sugo.track\(event\_id, event\_name, props\); // 准备把自定义事件发送到服务器时

