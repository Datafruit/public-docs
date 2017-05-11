# WEB可视化埋点

1. 点击项目管理，数据接入

2. 点击对应web项目数据接入，到管理页面

3. 点击可视化配置，如图所示

![](/assets/ksh/1.png)

4.选择对应的站点进入可视化埋点

![](/assets/ksh/2.png)

![](/assets/ksh/3.png)

## 埋点操作

1、点击创建事件按钮可进入圈选元素状态

![](/assets/ksh/4.png)

2、选中埋点元素点击左键弹出埋点对话框，设置埋点参数

![](/assets/ksh/5.png)

### 弹出框参数说明

* 页面：当前埋点的页面路径

* 元素：当前选中的埋点元素

* 同类元素：同一个类型的元素，比如是列表或者是循环变量出来的元素都可以只埋点其中一个元素，其他同类按当前埋点的处理，名称需填写一致，则数据将会合并。

* 类型：数据埋点事件类型，目前支持点击\(click\)、聚焦\(focus\)、改变\(change\)、提交\(submit\)。

* 名称：数据埋点事件名称，默认为选中元素的TEXT，可自定义。注意需要认真填写易懂的名称，用于精准的数据分析。

* 代码：数据埋点代码注入，后面详细介绍。

### 查看和管理已创建事件

![](/assets/ksh/6.png)

![](/assets/ksh/8.png)
### 浏览参数设置

* 在当前页面的浏览事件上报设置页面名称以及可代码注入其他属性维度。

* 事件部署：将已创建事件发布部署到正式环境以供客户端拉取配置绑定事件。

![](/assets/ksh/7.png)

## 代码注入

需要有一定js基础，可通过自定义获取页面元素某些值作为上报的自定义属性

// 函数自带了 \`e\`, \`element\`, \`conf\`, \`instance\`这些参数, 可以直接调用执行变量。例如：

var text = element.innerText; // 获取当前点击元素的text

//假设xxx为一个input框的id，我们又想在点击元素时同时上报这个input的value值。

var custom1 = document.getElementById\('xxx'\).value

// 这里写的是一个函数内容,最终返回为json对象的自定义属性

return {EventText: text, Custom1: custom1}

### 自带参数说明

•    浏览参数设置的代码注入自带参数为：conf, instance

    o  conf: 页面参数设置对象：就是弹出框保存的内容，包含页面名称和代码属性
    o  instance: sugoio对象

•    点击元素埋点时的代码注入自带参数为：e, element, conf, instance

    o  e: 当前点击事件的EVENT JS DOM对象
    o  element: 当前点击DOM元素，是一个JS DOM对象
    o  conf: 埋点弹出框设置那些属性对象，包含页面、元素、事件、同类元素等。
    o  instance: sugoio对象

