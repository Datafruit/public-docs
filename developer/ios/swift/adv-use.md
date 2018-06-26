## 3. SDK的进阶调用

### 3.1 获取全局对象

通过单例模式获取全局可用的对象，如下：

```
let sugo = Sugo.mainInstance()
```

### 3.2 手动埋点

#### 3.2.1 代码埋点

当需要把自定义事件发送到服务器时，可在相应位置调用以下API

* `open func track(eventID: String? = nil, eventName: String?, properties: Properties? = nil)`

示例如下(根据使用需求调用)：

```
Sugo.mainInstance().track(eventName: "EventName")
Sugo.mainInstance().track(eventName: "EventName", properties: ["key1": "value1", "key2": "value2"])
Sugo.mainInstance().track(eventID: "EventId", eventName: "EventName")	//eventId可为空
Sugo.mainInstance().track(eventID: "EventId", eventName: "EventName", properties: ["key1": "value1", "key2": "value2"])	//eventId可为空
```

#### 3.2.2 时长统计

##### 3.2.2.1 创建

当需要对时间进行跟踪统计时，可在开始跟踪的位置调用

* `open func time(event: String)`

示例如下：

```
Sugo.mainInstance().time(event: "TimeEventName")
```

##### 3.2.2.2 发送

然后，在完成跟踪的位置调用`3.2.1`中的方法即可，需要注意的是`eventName`需要与开始时的一样，示例如下：

```
Sugo.mainInstance().track(eventName: "TimeEventName")
```

##### 3.2.2.3 更新

如果在创建时间跟踪后，在发送前想要更新，再次以相同的时间事件名调用创建时的API即可，SDK会把相同事件名的记录时间进行刷新。

##### 3.2.2.4 删除

如果希望清除所有时间跟踪事件，可以通过调用

* `open func clearTimedEvents()`

示例如下：

```
Sugo.mainInstance().clearTimedEvents()
```

#### 3.2.3 全局属性

##### 3.2.3.1 注册

当每一个事件都需要记录相同的属性时，可以选择使用全局属性(全局属性仅允许`String`类型作为key值，value值则需要符合`SugoType`类型，而`SugoType`类型包括`String`, `Int`, `UInt`, `Double`, `Float`, `Bool`, `[SugoType]`, `[String: SugoType]`, `Date`, `URL`, 和`NSNull`)，
通过调用

* `open func registerSuperPropertiesOnce(_ properties: Properties, defaultValue: SugoType? = nil)`
* `open func registerSuperProperties(_ properties: Properties)`

示例如下：

```
Sugo.mainInstance().registerSuperPropertiesOnce(["key": "value"])    // 此方法不会覆盖当前已有的全局属性
Sugo.mainInstance().registerSuperProperties(["key": "value"])    // 此方法会覆盖当前已有的全局属性
```

##### 3.2.3.2 获取

当需要获取已注册的全局属性时，可以调用

* `open func currentSuperProperties() -> [String: Any]`

示例如下：

```
Sugo.mainInstance().currentSuperProperties()
```

##### 3.2.3.3 注销

当需要注销某一全局属性时，可以调用

* `open func unregisterSuperProperty(_ propertyName: String)`

示例如下：

```
Sugo.mainInstance().unregisterSuperProperty("key")
```

##### 3.2.3.4 清除

当需要清除所有全局属性时，可以调用

* `open func clearSuperProperties()`

示例如下：

```
Sugo.mainInstance().clearSuperProperties()
```

#### 3.2.3.5 跟踪用户首次登录

当需要跟踪用户首次登录用户账户时，可调用

* `open func trackFirstLogin(with id: String, dimension: String)`

示例如下(其中`dimension`参数为用户已自定义的维度名)：

```
Sugo.mainInstance().trackFirstLogin(with: "userId", dimension: "userIdDimension")
```

#### 3.2.4 WebView埋点

当需要在WebView(UIWebView或WKWebView)中进行代码埋点时，在页面加载完毕后，可调用以下API(是`3.2.1`与`3.2.2`同名方法在JavaScript中的接口，实现机制相同)进行JavaScript内容的代码埋点

```
sugo.track(event_id, event_name, props);    // 准备把自定义事件发送到服务器时
sugo.timeEvent(event_name);	                // 在开始统计时长的时候调用
```

#### 3.2.5 Weex埋点

当需要在Weex(Vue)中进行代码埋点时，可调用以下API(是`3.2.1`与`3.2.2`同名方法在Weex中的接口，实现机制相同)进行JavaScript的代码埋点

```
let sugo = weex.requireModule('sugo');
sugo.track(event_name, props);              // 准备把自定义事件发送到服务器时
sugo.timeEvent(event_name);                 // 在开始统计时长的时候调用
```

* 需要为项目创建一个用户库以存放用户登录信息，注意如没有设置将无法存储用户上报的登录信息。相关说明参照 [用户库功能](/project-management.md#user-library)