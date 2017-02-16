#### 手动安装

为了帮助开发者集成最新且稳定的SDK，我们建议通过Cocoapods来集成，这不仅简单而且易于管理。 然而，为了方便其他集成状况，我们也提供手动安装此SDK的方法。

**1.以子模块的形式添加**

以子模块的形式把sugo-objc-sdk添加进本地仓库中:

git submodule add git@github.com:Datafruit/sugo-objc-sdk.git

现在在仓库中能看见Sugo项目文件Sugo.xcodeproj了。

**2.把Sugo.xcodeproj拖到你的项目（或工作空间）中**

把Sugo.xcodeproj拖到需要被集成使用的项目文件中。

**3.嵌入框架（Embed the framework）**

选择需要被集成此SDK的项目target，把Sugo.framework以embeded binary形式添加进去。