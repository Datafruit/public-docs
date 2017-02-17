# 预置属性
```javascript

\['sugo\_nation', '国家'\], //用户所在国家\(ip反解析\)

\['sugo\_province', '省份'\], //用户所在省份\(ip反解析\)

\['sugo\_city', '城市'\], //用户所在城市\(ip反解析\)

\['sugo\_district', '地区'\], //用户所在地区\(ip反解析\)

\['sugo\_area', '区域'\], //用户所在区域\(ip反解析\)

\['sugo\_latitude', '纬度'\], //纬度\(ip反解析\)

\['sugo\_longitude', '经度'\], //经度\(ip反解析\)

\['sugo\_city\_timezone', '城市时区'\], //所在时区代表城市\(ip反解析\)

\['sugo\_timezone', '时区'\], //所在时区\(ip反解析\)

\['sugo\_phone\_code', '国际区号'\], //国际区号\(ip反解析\)

\['sugo\_nation\_code', '国家代码'\], //国家代码\(ip反解析\)

\['sugo\_continent', '所在大洲'\], //所在大洲\(ip反解析\)

\['sugo\_administrative', '行政区划代码'\], //中国行政区划代码\(ip反解析\)

\['sugo\_operator', '运营商'\], //用户所在运营商\(ip反解析\)

\['sugo\_ip', '客户端IP'\], //客户端IP\(nginx\)

\['sugo\_http\_forward', '客户端真实IP'\], //客户端真实ip\(nginx\)

\['sugo\_http\_refer', 'Referer'\], //Referer\(nginx\)

\['sugo\_user\_agent', '浏览器标识'\], //浏览器标识\(nginx\)

\['brower, '浏览器名称'\], //浏览器名称

\['brower\_version', '浏览器版本'\], //浏览器版本

\['sugo\_args', '请求参数'\], //请求参数\(nginx\)

\['sugo\_http\_cookie', 'HttpCookie'\], //HttpCookie\(nginx\)

\['app\_name', '系统名称'\], //系统或app的系统名称

\['app\_version', '系统版本'\], //系统或app的系统版本

\['app\_build\_number', 'android build number'\], //android build number

\['session\_id','会话ID'\], //会话id

\['network', '网络类型'\], //用户使用的网络

\['device\_id','设备ID'\], //浏览器cookies（首次访问时生成\)/Android或ios device id

\['bluetooth\_version', '蓝牙版本'\], //用户蓝牙版本

\['has\_bluetooth', '蓝牙版本'\], //用户是否有蓝牙

\['device\_brand', '品牌'\], //用户电脑、平板、或手机牌子

\['device\_model', '品牌型号'\], //用户电脑、平板、或手机型号

\['system\_name', '浏览器/android/ios'\], //用户系统、平板或手机OS

\['system\_version', '操作系统版本'\], //用户平板或手机OS版本

\['radio', '通信协议'\], //通信协议

\['carrier', '运营商'\], //运营商

\['screen\_dpi', '分辨率'\], //客户端分辨率

\['screen\_height', '屏幕高度'\], //客户端屏幕高度

\['screen\_width', '屏幕宽度'\], //客户端屏幕宽度

\['event\_time', '客户端事件时间', 4\], //客户端事件发生时间（unix毫秒数\)

\['current\_url', '当前请求地址'\], //客户端当前请求地址

\['referrer', '客户引荐'\], //客户引荐

\['referring\_domain', '客户引荐域名'\], //客户引荐域名

\['host', '客户端域名'\], //客户端域名

\['distinct\_id', '用户唯一ID'\], //用户唯一ID

\['has\_nfc', 'NFC功能'\], //NFC功能

\['has\_telephone', '电话功能'\], //电话功能

\['has\_wifi', 'WIFI功能'\], //wifi功能

\['manufacturer', '设备制造商'\], //设备制造商

\['duration', '停留时间', 1\], //页面停留时间

\['sdk\_version', 'SDK版本'\], //sdk版本

\['page\_name', '页面名称'\], //页面名称或屏幕名称

\['path\_name', '页面路径'\], //页面路径

\['event\_id', '事件ID'\], //事件ID

\['event\_name', '事件名称'\], //事件名称

\['event\_type', '事件类型'\], //事件类型click、focus、submit、change

\['event\_label', '事件源文本'\], //事件源文本

\['sugo\_lib', 'sdk类型'\], //sdk类型 web、ios、android

\['token', '应用ID'\], //应用ID

\['from\_binding', '是否绑定事件'\], //是否绑定事件（用于区分是绑定的，还是系统自动上报的，比如浏览、启动为自动上报，绑定取值1，自动上报取值0）

\['google\_play\_services', 'GooglePlay服务'\], //GooglePlay服务

```