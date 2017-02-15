### 异步载入

&lt;head&gt;

...

_&lt;!-- start sugoio --&gt;_

&lt;script type=&#039;text/javascript&#039;&gt;

(**function**(e,a){**if**(!a.__SV){**var** b=window;**try**{**var** c,n,k,l=b.location,g=l.hash;c=**function**(a,b){**return**(n=a.match(**new** RegExp(b+&quot;=([^&amp;]*)&quot;)))?n[1]:null};g&amp;&amp;c(g,&quot;state&quot;)&amp;&amp;(k=JSON.parse(decodeURIComponent(c(g,&quot;state&quot;))),&quot;mpeditor&quot;===k.action&amp;&amp;(b.sessionStorage.setItem(&quot;_mpcehash&quot;,g),history.replaceState(k.desiredHash||&quot;&quot;,e.title,l.pathname+l.search)))}**catch**(p){}**var** m,h;window.sugoio=a;a._i=[];a.init=**function**(b,c,f){**function** **e**(b,a){**var** c=a.split(&quot;.&quot;);2==c.length&amp;&amp;(b=b[c[0]],a=c[1]);b[a]=**function**(){b.push([a].concat(Array.prototype.slice.call(arguments,

0)))}}**var** d=a;&quot;undefined&quot;!==**typeof** f?d=a[f]=[]:f=&quot;sugoio&quot;;d.people=d.people||[];d.toString=**function**(b){**var** a=&quot;sugoio&quot;;&quot;sugoio&quot;!==f&amp;&amp;(a+=&quot;.&quot;+f);b||(a+=&quot; (stub)&quot;);**return** a};d.people.toString=**function**(){**return** d.toString(1)+&quot;.people (stub)&quot;};m=&quot;disable time_event track track_pageview track_links track_forms register register_once alias unregister identify name_tag set_config reset people.set people.set_once people.increment people.append people.union people.track_charge people.clear_charges people.delete_user&quot;.split(&quot; &quot;);

**for**(h=0;h&lt;m.length;h++)e(d,m[h]);a._i.push([b,c,f])};a.__SV=1.2;b=e.createElement(&quot;script&quot;);b.type=&quot;text/javascript&quot;;b.async=!0;&quot;undefined&quot;!==**typeof** SUGOIO_CUSTOM_LIB_URL?b.src=SUGOIO_CUSTOM_LIB_URL:b.src=&quot;file:&quot;===e.location.protocol&amp;&amp;&quot;//localhost:8080/_bc/sugo-sdk-js/libs/sugoio-latest.min.js&quot;.match(/^\/\//)?&quot;https://localhost:8080/_bc/sugo-sdk-js/libs/sugoio-latest.min.js&quot;:&quot;//localhost:8080/_bc/sugo-sdk-js/libs/sugoio-latest.min.js&quot;;c=e.getElementsByTagName(&quot;script&quot;)[0];c.parentNode.insertBefore(b,

c)}})(document,window.sugoio||[]);

sugoio.init(&#039;YOUR_TOKEN&#039;, {&#039;project_id&#039;: &#039;YOUR_PROJECT_ID&#039;});

&lt;/script&gt;

_&lt;!-- end sugoio --&gt;_

...

&lt;/head&gt;

以上片段将异步方式加载我们库到页面，并提供了一个全局变量名为sugoio，你可以用它来代码埋点上报数据。 成功加载SDK后数果智能会自动采集页面浏览量。我们还提供一些高级设置：用户属性、页面属性、代码注入等。具体设置方法请参考下面的详细说明。