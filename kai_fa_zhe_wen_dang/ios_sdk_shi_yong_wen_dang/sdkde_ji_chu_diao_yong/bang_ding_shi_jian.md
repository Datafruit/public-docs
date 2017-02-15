#### 绑定事件

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