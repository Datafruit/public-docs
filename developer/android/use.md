# SDK 使用

标准的使用实例，应该是在 APP 启动的第一个 `Activity` 中，调用 `SugoAPI.startSugo`   

```Java   

public class MainActivity extends Activity {

    SugoAPI mSugo;

    public void onCreate(Bundle saved) {
        // SDK 将会初始化，此处若设置 Token 、 ProjectId，将覆盖 AndroidManifest 中的设置
        SugoAPI.startSugo(this, SGConfig.getInstance(this)
            .setToken("38c07f58b8f6e1df82ea29f794b6e097")
            .setProjectId("com_HyoaKhQMl_project_B1GjJOHFg")
            .logConfig());

        // 获取 SugoAPI 实例
        mSugo = SugoAPI.getInstance(this);
        ...
    }

    // 使用 SugoAPI.track 方法来记录事件
    public void whenSomethingInterestingHappens(int flavor) {
        JSONObject properties = new JSONObject();
        properties.put("flavor", flavor);
        mSugo.track("Something Interesting Happened", properties);
        ...
    }

    public void onDestroy() {
        // 上传数据
        mSugo.flush();      // 非必须，仅推荐使用
        super.onDestroy();
    }
}
```   

