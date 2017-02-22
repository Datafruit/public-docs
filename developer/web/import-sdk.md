# 异步载入
```javascript
<!-- start sugoio -->
  <script type='text/javascript'>
    (function(e,a){if(!a.__SV)
    	{var b=window;
    		try{var c,n,k,l=b.location,g=l.hash;
    		c=function(a,b){return(n=a.match(new RegExp(b+"=([^&]*)")))?n[1]:null};
    	g&&c(g,"state")&&(k=JSON.parse(decodeURIComponent(c(g,"state"))),
    		"mpeditor"===k.action&&(b.sessionStorage.setItem("_mpcehash",g),
    			history.replaceState(k.desiredHash||"",e.title,l.pathname+l.search)))}
    	catch(p){}
    	var m,h;window.sugoio=a;a._i=[];
    	a.init=function(b,c,f){function e(b,a){var c=a.split(".");2==c.length&&(b=b[c[0]],a=c[1]);b[a]=function(){b.push([a].concat(Array.prototype.slice.call(arguments,
    0)))}}
    	var d=a;"undefined"!==typeof f?d=a[f]=[]:f="sugoio";d.people=d.people||[];
    	d.toString=function(b){var a="sugoio";"sugoio"!==f&&(a+="."+f);b||(a+=" (stub)");return a};
    	d.people.toString=function(){return d.toString(1)+".people (stub)"};
    	m="disable time_event track track_pageview track_links track_forms register register_once alias unregister identify name_tag set_config reset people.set people.set_once people.increment people.append people.union people.track_charge people.clear_charges people.delete_user".split(" ");
    for(h=0;h<m.length;h++)e(d,m[h]);a._i.push([b,c,f])};
    	a.__SV=1.2;b=e.createElement("script");
    b.type="text/javascript";b.async=!0;
    "undefined"!==typeof SUGOIO_CUSTOM_LIB_URL?b.src=SUGOIO_CUSTOM_LIB_URL:
    b.src="file:"===e.location.protocol&&"//192.168.0.212:8000/_bc/sugo-sdk-js/libs/sugoio-latest.min.js"
    .match(/^\/\//)?"https://192.168.0.212:8000/_bc/sugo-sdk-js/libs/sugoio-latest.min.js":"//192.168.0.212:8000/_bc/sugo-sdk-js/libs/sugoio-latest.min.js";
    c=e.getElementsByTagName("script")[0];c.parentNode.insertBefore(b,c)}})
    (document,window.sugoio||[]);
    sugoio.init('f2e650cf8c89a72e3368fa6020276f5b', {'project_id': 'com_HyoaKhQMl_project_rJ4uImQKg'});
  </script>
<!-- end sugoio -->
```

以上片段将异步方式加载我们库到页面，并提供了一个全局变量名为sugoio，你可以用它来代码埋点上报数据。 成功加载SDK后数果智能会自动采集页面浏览量。我们还提供一些高级设置：用户属性、页面属性、代码注入等。具体设置方法请参考下面的详细说明。

