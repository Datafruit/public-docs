### SDK 使用 {#sdk}

标准的使用实例，应该是在 APP 启动的第一个Activity中，添加以下代码

**public** **class** **MainActivity** **extends** **Activity** {

SugoAPI mSugo;

**public** **void** **onCreate**(Bundle saved) {

_// 获取 SugoAPI 实例，在第一次调用时，SDK 将会初始化_

mSugo = SugoAPI.getInstance(**this**);

...

}

_// 使用 SugoAPI.track 方法来记录事件_

**public** **void** **whenSomethingInterestingHappens**(**int** flavor) {

JSONObject properties = **new** JSONObject();

properties.put(&quot;flavor&quot;, flavor);

mSugo.track(&quot;Something Interesting Happened&quot;, properties);

...

}

**public** **void** **onDestroy**() {

_// 如果启动的 Activity 不是最后销毁的 Activity，_

_// 这行代码可以移到对应的最后销毁的 Activity 的 onDestroy 方法里_

mSugo.flush(); _// 非必须，仅推荐使用_

**super**.onDestroy();

}

}