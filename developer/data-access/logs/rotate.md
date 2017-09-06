## 日志切割

当日志文件日渐增大，可把日志文件切割成多文件，从而便于作额外处理。

在日志文件目录中，创建一个`rotate.sh`文件，并把以下内容粘贴进去。

```
#!/bin/sh

source /etc/profile

LOG_PATH=$1
LOG_NAME=$2
LAST_DATE=$(date -d "1 minute ago" +%Y%m%d%H%M%S)
mv ${LOG_PATH}/${LOG_NAME} ${LOG_PATH}/${LOG_NAME}.${LAST_DATE}.log

kill -HUP `ps aux | grep "rsyslogd" | grep -v grep | awk '{print $2}'`

cd ${LOGS_PATH}
find . -mtime +7 -name "${LOG_NAME}.*.log" | xargs rm -f
exit 0
```

`rotate.sh`将对日志记录进行切割，以时间为后缀进行区分，并默认删除超过7天的旧日志。

然后执行以下命令：

```
sudo chmod 700 rotate.sh
```

为了日志切割能定时执行，需要执行以下命令：

```
sudo crontab -e
```

并把以下内容在调整路径后添加进去并保存。

```
* * * * * /LOG_PATH/rotate.sh /LOG_PATH LOG_NAME >>  /LOG_PATH/rotate.log 2>&1
```

在触发执行日志切割后，可通过日志文件目录里产生的`rotate.log`查看运行状态。
