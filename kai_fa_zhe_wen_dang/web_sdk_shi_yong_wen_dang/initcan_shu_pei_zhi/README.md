### init参数配置 {#init}

sugoio.init(&#039;YOUR_TOKEN&#039;, { // 项目TOKEN

project_id: &#039;YOUR_PROJECT_ID&#039;, // 项目ID

api_host: &#039;&#039;, // sugoio-latest.min.js文件以及数据上报的地址

app_host: &#039;&#039;, // 可视化配置时服务端地址

decide_host: &#039;&#039;, // 加载已埋点配置地址

loaded: function(**lib**) { }, // **sugoio** **sdk** 加载完成回调函数

dimensions: { }, // 上报维度自定义映射配置参数

DEBUG: false // 是否启用debug

});

*   **YOUR_TOKEN：** 为项目TOKEN。
*   **YOUR_PROJECT_ID：** 为项目ID。
*   **api_host：** sugoio-latest.min.js文件以及数据上报的地址。
*   **app_host：** 可视化配置时服务端地址。
*   **decide_host：** 加载已埋点配置地址。
*   **loaded：** sugoio sdk 加载完成回调函数。
*   **dimensions：** 上报维度自定义映射配置参数， 下文详细说明。
*   **DEBUG：** 是否启用debug。