
## <a id="download" href="download"></a> 下载部署sugo-plyql

- 可以在终端使用

```
wget http://58.63.110.97:8888/yum/sugo-plyql.tar.gz
```

- 也可以直接点击[sugo-plyql](sugo-plyql.tar.gz)下载


### 启动脚本说明

```shell
# 解压
tar xzf sugo-plyql.tar.gz

# 进入目录
cd sugo-plyql/cmds
```



#### 终端命令模式

```shell
./plyql -h 192.168.0.212 -q 'show tables'
```

#### HTTP REST API模式

 > - 启动PM2集群模式服务（启动前先修改druid的broker节点信息)
 > - broker信息位于./rest/pm2.yaml文件内的 **args:** 参数，如：`-h 192.168.0.212 --json-server 8001`  
 > - 可自定义修改`host`和`port`参数

```shell
# 启动rest服务
./rest/run

# 停止rest服务
./rest/stop

#重启rest服务
./rest/reload
```

> 启动rest服务后可发送post请求到：**`http://192.168.0.212:8001/plyql`**
  > - 参数格式：
  ```
    {
      "sql": "show tables" // 这里为sql查询语句
    }
  ```

#### JDBC驱动模式

 > - 启动PM2集群模式服务（启动前先修改druid的broker节点信息)
 > - broker信息位于./jdbc/pm2.yaml文件内的 **args:** 参数，如：`-h 192.168.0.212 --experimental-mysql-gateway 13307`  
 > - 可自定义修改`host`和`port`参数

```shell
# 启动jdbc服务
./jdbc/run

# 停止jdbc服务
./jdbc/stop

#重启jdbc服务
./jdbc/reload
```
> 启动mysql驱动服务后可以用mysql客户端工具连接执行sql查询  
  > - host: 192.168.0.212  
  > - port: 13307  
  > - user: root  
  > - password: ''  