# 数据库接入

## 1．添加数据接入方式》MySql接入》下一步

![](/assets/sjkjr/1.png)

## 2.填写数据库配置信息

![](/assets/sjkjr/2.png)

## 3.下载采集程序，根据操作文档部署采集程序

![](/assets/sjkjr/3.png)

## 4.mysql配置

sugo-canal的原理是基于mysql binlog技术，所以这里一定需要开启mysql的binlog写入功能，建议配置binlog模式为row.

\[mysqld\]

log-bin=mysql-bin \#添加这一行就ok

binlog-format=ROW \#选择row模式

server\_id=1 \#配置mysql replaction需要定义，不能和canal的slaveId重复

sugo-canal的原理是模拟自己为mysql slave，所以这里一定需要做为mysql slave的相关权限

CREATE USER canal IDENTIFIED BY 'root';

GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT ON \*.\* TO 'root'@'%';

-- GRANT ALL PRIVILEGES ON \*.\* TO 'root'@'%' ; \# 针对已有的账户可通过grant

FLUSH PRIVILEGES;

