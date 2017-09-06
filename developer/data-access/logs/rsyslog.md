## rsyslog集中化nginx日志

[nginx](http://nginx.org/)自`1.7.1`后，支持将日志记录到syslog中，这将意味着可通过[rsyslog](http://www.rsyslog.com/)，把多个nginx产生的日志集中在一个位置，若nginx版本小于`1.7.1`，推荐使用更方便的Sugo-C收集nginx日志。

在使用此功能前，需满足以下条件：

1. 服务端与客户端之间可连通
2. 客户端安装了nginx，且nginx配置了`ngx_http_log_module`
3. 服务端安装了rsyslog

#### 服务端配置

在服务端中（*假设IP地址为192.168.0.221*），需要在`rsyslog.conf`（*默认存放于/etc目录下*）或于`rsyslog.d`目录下创建`*.conf`文件配置，配置例子如下：

```
#### MODULES ####

$ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
$ModLoad imklog   # provides kernel logging support (previously done by rklogd)
#$ModLoad immark  # provides --MARK-- message capability

# 加载UDP模块及打开接口
# Provides UDP syslog reception
$ModLoad imudp
$UDPServerRun 514

# 加载TCP模块及打开接口
# Provides TCP syslog reception
#$ModLoad imtcp
#$InputTCPServerRun 514


#### GLOBAL DIRECTIVES ####

# 可通过自定义默认Template来记录日志的格式，若希望保持原日志格式，可使用以下配置进行替换
# $template originalFormat,"%msg%\n"
# $ActionFileDefaultTemplate originalFormat
# 若不希望改动全部日志格式，可在facility设置的路径后添加";originalFormat"，例子如下：
# *.*                                                 /path/to/log;originalFormat

# Use default timestamp format
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat

# File syncing capability is disabled by default. This feature is usually not required,
# not useful and an extreme performance hit
#$ActionFileEnableSync on

# Include all config files in /etc/rsyslog.d/
$IncludeConfig /etc/rsyslog.d/*.conf

# rules中可填写自定义selector，由facility和level组成，以"."分隔，若填写多个，则以“;”分隔
#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none;local0.none    /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 *

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log

# 与客户端facility对应，配置日志文件存放地址
local0.*                                                /var/log/rsyslog.local0

# ### begin forwarding rule ###
# The statement between the begin ... end define a SINGLE forwarding
# rule. They belong together, do NOT split them. If you create multiple
# forwarding rules, duplicate the whole block!
# Remote Logging (we use TCP for reliable delivery)
#
# An on-disk queue is created for this action. If the remote host is
# down, messages are spooled to disk and sent when it is up again.
#$WorkDirectory /var/lib/rsyslog # where to place spool files
#$ActionQueueMaxDiskSpace 1g   # 1gb space limit (use as much as possible)
#$ActionQueueSaveOnShutdown on # save messages to disk on shutdown
#$ActionQueueType LinkedList   # run asynchronously
#$ActionResumeRetryCount -1    # infinite retries if host is down
# remote host is: name/ip:port, e.g. 192.168.0.1:514, port optional
#*.* @@remote-host:514
# ### end of the forwarding rule ###
```

facility：

* auth(security), authpriv: 授权和安全相关的消息
* kern: 来自Linux内核的消息
* mail: 由mail子系统产生的消息
* cron: cron守护进程相关的信息
* daemon: 守护进程产生的信息
* news: 网络消息子系统
* lpr: 打印相关的日志信息
* user: 用户进程相关的信息
* local0 ~ local7: 保留，本地使用

level：

* debug：包含详细的开发情报的信息，通常只在调试一个程序时使用。
* info：情报信息，正常的系统消息，比如骚扰报告，带宽数据等，不需要处理。
* notice： 不是错误情况，也不需要立即处理。
* warning： 警告信息，不是错误，比如系统磁盘使用了85%等。
* err：错误，不是非常紧急，在一定时间内修复即可。
* crit：重要情况，如硬盘错误，备用连接丢失。
* alert：应该被立即改正的问题，如系统数据库被破坏，ISP连接丢失。
* emerg：紧急情况，需要立即通知技术人员。

配置完成后，可通过命令行`/etc/init.d/rsyslog restart`重启。

#### 客户端配置

在客户端（*假设IP地址为192.160.0.220*），只需要配置nginx的配置文件`nginx.conf`，具体配置参数可参考[Logging to syslog](http://nginx.org/en/docs/syslog.html)，配置例子如下：


```
	
	log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

	# 原日志配置，若保留，则可在对应目录内查看客户端本地日志
    access_log  logs/access.log main;

    # syslog日志配置
    access_log  syslog:server=192.168.0.221:514,facility=local0,tag=nginx,severity=info main;

```

配置完成后，重新启动nginx即可。

```
nginx -s quit
nginx
```
