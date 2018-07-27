# 预置属性

|维度名|维度标题|类型|iOS|Android|Web|维度描述|
|:---|:---|:---:|:---:|:---:|:---:|:---|
|__time               | 服务端时间 | date | √ | √ | √ | 服务端时间 |
|sugo_nation          | 国家 | string | √ | √ | √ | 用户所在国家(ip反解析) |
|sugo_province        | 省份 | string | √ | √ | √ | 用户所在省份(ip反解析) |
|sugo_city            | 城市 | string | √ | √ | √ | 用户所在城市(ip反解析) |
|sugo_district        | 地区 | string | √ | √ | √ | 用户所在地区(ip反解析) |
|sugo_area            | 区域 | string | √ | √ | √ | 用户所在区域(ip反解析) |
|sugo_latitude        | 纬度 | string | √ | √ | √ | 纬度(ip反解析) |
|sugo_longitude       | 经度 | string | √ | √ | √ | 经度(ip反解析) |
|sugo_city_timezone   | 城市时区 | string | √ | √ | √ | 所在时区代表城市(ip反解析) |
|sugo_timezone        | 时区 | string | √ | √ | √ | 所在时区(ip反解析) |
|sugo_phone_code      | 国际区号 | string | √ | √ | √ | 国际区号(ip反解析) |
|sugo_nation_code     | 国家代码 | string | √ | √ | √ | 国家代码(ip反解析) |
|sugo_continent       | 所在大洲代码 | string | √ | √ | √ | 所在大洲(ip反解析) |
|sugo_administrative  | 行政区划代码 | string | √ | √ | √ | 中国行政区划代码(ip反解析) |
|sugo_operator        | 宽带运营商 | string | √ | √ | √ | 用户所在运营商(ip反解析) |
|sugo_ip              | 客户端IP | string | √ | √ | √ | 客户端IP(nginx)Header的X-Real-IP，或者 remote_addr |
|browser              | 浏览器名称 | string | x | x | √ | 浏览器名称 |
|browser_version      | 浏览器版本 | string | x | x | √ | 浏览器版本 |
|app_name             | 应用名称 | string | √ | √ | x | 系统或app的系统名称（应用安装后的名字） |
|app_version          | 应用版本 | string | √ | √ | x | 系统或app的系统版本 |
|app_build_number     | 应用编译版本 | string | √ | √ | x | 应用编译版本 |
|session_id           | 会话ID | string | √ | √ | √ | 会话id（app每次打开自动生成，web每次打开浏览器自动生成，如果不关闭浏览器，一天后自动生成新的session_id） |
|network              | 网络类型 | string | √ | √ | x | 用户使用的网络类型（wifi,2g,3g,4g） |
|device_id            | 设备ID | string | √ | √ | x | Android: IMEI > mac > android_id/iOS: IDFA > IDFV |
|bluetooth_version    | 蓝牙版本 | string | x | √ | x | 用户蓝牙版本 |
|has_bluetooth        | 蓝牙功能 | string | x | √ | x | 用户是否有蓝牙 |
|device_brand         | 品牌 | string | √ | √ | √ | 用户电脑、平板、或手机牌子 |
|device_model         | 品牌型号 | string | √ | √ | √ | 用户电脑、平板、或手机型号 |
|system_name          | 操作系统名称 | string | √ | √ | √ | 客户端操作系统名称（Android，iOS, Windows, macOS 等） |
|system_version       | 操作系统版本 | string | √ | √ | √ | 客户端操作系统版本 |
|radio                | 通信协议 | string | √ | √ | x | 通信协议（gsm，cdma,sip等） |
|carrier              | 手机运营商 | string | √ | √ | x | 运营商（中国移动，中国联通等） |
|screen_dpi           | 分辨率 | int | x | √ | √ | 客户端分辨率（dpi） |
|screen_height        | 屏幕高度 | int | √ | √ | √ | 客户端屏幕高度（高度方向上像素点的个数） |
|screen_width         | 屏幕宽度 | int | √ | √ | √ | 客户端屏幕宽度（宽度方向上像素点的个数） |
|event_time           | 客户端事件时间 | date | √ | √ | √ | 客户端事件发生时间（unix毫秒数) |
|current_url          | 当前请求地址 | string | x | x | √ | 客户端当前请求地址 |
|referring_domain     | 客户引荐域名 | string | x | x | √ | 客户引荐域名（上一访问页面地址栏域名） |
|host                 | 客户端域名 | string | x | x | √ | 浏览器地址栏域名 |
|distinct_id          | 用户唯一ID | string | √ | √ | √ | web首次访问生成用户唯一id并存在cookies，清除cookies算一个新的用户，app首次打开时候会生成一个用户唯一id，重装算另一个用户 |
|has_nfc              | NFC功能 | string | x | √ | x | 是否有NFC功能 |
|has_telephone        | 电话功能 | string | x | √ | x | 是否有电话功能 |
|has_wifi             | WIFI功能 | string | √ | √ | x | 是否有wifi功能 |
|manufacturer         | 设备制造商 | string | √ | √ | x | 设备制造商（meizu，huawei等） |
|duration             | 停留时间 | float | √ | √ | √ | 页面停留时间(停留事件才有) |
|sdk_version          | SDK版本 | string | √ | √ | √ | sdk版本 |
|page_name            | 页面名称 | string | √ | √ | √ | 页面名称或窗口名称，可在可视化埋点界面设置，默认取title |
|path_name            | 页面路径 | string | √ | √ | √ | 页面路径（web取域名之后的路径） |
|event_id             | 事件ID | string | √ | √ | √ | 事件ID,可视化埋点绑定的事件才上报 |
|event_name           | 事件名称 | string | √ | √ | √ | 事件名称 |
|event_type           | 事件类型 | string | √ | √ | √ | 事件类型click、focus、submit、change |
|event_label          | 事件源文本 | string | x | √ | √ | 事件源文本（如按钮上的文字） |
|sugo_lib             | sdk类型 | string | √ | √ | √ | sdk类型: web/Objective-C/Swift/Android/Wx-mini |
|token                | 应用ID | string | √ | √ | √ | 应用ID |
|from_binding         | 是否绑定事件 | string | √ | √ | x | 是否绑定事件（null或者true）（用于区分是绑定的，还是系统自动上报的，比如浏览、启动为自动上报，绑定取值1，自动上报取值0） |
|google_play_service  | GooglePlay服务 | string | x | √ | x | 是否有GooglePlay服务 |
|page_category        | 页面分类 | string | √ | √ | √ | 页面分类 |
|first_visit_time     | 首次访问时间 | date | √ | √ | √ | 首次访问时间（用户唯一ID的首次访问时间） |
|first_login_time     | 首次登陆时间 | date | √ | √ | √ | 首次登陆时间（业务ID的首次登录时间，需要把业务ID 传给sdk） |