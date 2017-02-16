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