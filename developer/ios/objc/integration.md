# 集成

## CocoaPods

**现时我们的发布版本只能通过Cocoapods 1.1.0及以上的版本进行集成**

通过[CocoaPods](https://cocoapods.org/)，可方便地在项目中集成此SDK。

### **1.配置Podfile**

请在项目根目录下的`Podfile` （如无，请创建或从我们提供的SugoDemo目录中[获取](https://github.com/Datafruit/sugo-objc-sdk/blob/master/SugoDemo/Podfile)并作出相应修改）文件中添加以下字符串：

```
pod 'sugo-objc-sdk'
```

### **2.执行集成命令**

关闭Xcode，并在`Podfile`目录下执行以下命令：

```
pod install
```

### **3.完成**

运行完毕后，打开集成后的`xcworkspace`文件即可。

## 手动安装

为了帮助开发者集成最新且稳定的SDK，我们建议通过`Cocoapods`来集成，这不仅简单而且易于管理。 然而，为了方便其他集成状况，我们也提供手动安装此SDK的方法。

### **1.以子模块的形式添加**

以子模块的形式把`sugo-objc-sdk`添加进本地仓库中:

```
git submodule add git@github.com:Datafruit/sugo-objc-sdk.git
```

现在在仓库中能看见`Sugo`项目文件`Sugo.xcodeproj`了。

### **2.把Sugo.xcodeproj拖到你的项目（或工作空间）中**

把`Sugo.xcodeproj`拖到需要被集成使用的项目文件中。

### **3.嵌入框架（Embed the framework）**

选择需要被集成此SDK的项目target，把`Sugo.framework`以`embeded binary`形式添加进去。

