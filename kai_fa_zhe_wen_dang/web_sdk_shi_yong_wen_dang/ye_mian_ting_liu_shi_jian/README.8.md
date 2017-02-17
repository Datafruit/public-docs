# 页面停留事件

sugoio.init\('YOUR\_TOKEN', {

project\_id: 'YOUR\_PROJECT\_ID',

loaded: **function**\(lib\) {

sugoio.time\_event\('停留'\)

sugoio.\_.register\_event\(window, 'beforeunload', **function**\(\){

sugoio.track\('停留', {path\_name: location.pathname}\)

}, false, true\)

}

....

}\);

#### sugoio.register\_once\(object\) {#sugoio-register-once-object}

在 Cookie 中永久保存属性，如果存在这个属性了则不覆盖

