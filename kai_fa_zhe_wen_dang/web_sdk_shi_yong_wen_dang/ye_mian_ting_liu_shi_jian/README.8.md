### 页面停留事件

sugoio.init(&#039;YOUR_TOKEN&#039;, {

project_id: &#039;YOUR_PROJECT_ID&#039;,

loaded: **function**(lib) {

sugoio.time_event(&#039;停留&#039;)

sugoio._.register_event(window, &#039;beforeunload&#039;, **function**(){

sugoio.track(&#039;停留&#039;, {path_name: location.pathname})

}, false, true)

}

....

});