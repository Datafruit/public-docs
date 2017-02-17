### SDK 使用 {#sdk}

标准的使用实例，应该是在 APP 启动的第一个Activity中，添加以下代码
```javascript

**public** **class** **MainActivity** **extends** **Activity** {

SugoAPI mSugo;

**public** **void** **onCreate**(Bundle saved) {

// 获取 SugoAPI 实例，在第一次调用时，SDK 将会初始化_

mSugo = SugoAPI.getInstance(**this**);

...

}

// 使用 SugoAPI.track 方法来记录事件_

**public** **void** **whenSomethingInterestingHappens**(**int** flavor) {

JSONObject properties = **new** JSONObject();

properties.put("flavor", flavor);

mSugo.track("Something Interesting Happened", properties);

...

}

**public** **void** **onDestroy**() {

// 如果启动的 Activity 不是最后销毁的 Activity，_

// 这行代码可以移到对应的最后销毁的 Activity 的 onDestroy 方法里_

mSugo.flush(); // 非必须，仅推荐使用_

**super**.onDestroy();

}

}

```



