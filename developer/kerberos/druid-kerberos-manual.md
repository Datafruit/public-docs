
本文主要分为Kerberos内部集成与外部访问两大部分.

# 内部集成

## 一、前期准备

1. **Kerberos基本原理**  
&emsp;&emsp;在开始集成前,最好先了解Kerberos的基本概念和原理,这样可以在安装集成过程中对繁杂的配置和操作做到心里有数,遇到问题也明白如何表达以及如何利用Google、度娘搜索.Kerberos基本原理可点击[Kerberos传送门](http://blog.csdn.net/wulantian/article/details/42418231)进行了解.   
    
    &emsp;&emsp;总体而言,一个客户端使用kerberos获取服务时需要经过三个步骤:
    - **认证:** 客户端向认证服务器(KDC)发送一条报文，并获取一个含时间戳的 Ticket-Granting Ticket（TGT）.
    - **授权:** 客户端使用 TGT 向认证服务器(KDC) Ticket-Granting Server（TGS）请求一个服务 Ticket
    - **服务请求:** 客户端向目标服务器出示服务 Ticket ，以证实自己的合法性   

   &emsp;&emsp;为此,Kerberos需要The Key Distribution Centers（KDC）来进行认证.KDC只有一个Master，可以带多个 slaves机器(也可以没有slaves).slaves机器仅进行普通验证.Mater上做的修改需要自动同步到 slaves.
另外,KDC 需要一个 admin,来进行日常的管理操作.这个 admin 可以通过远程或者本地方式登录.

2. **Druid-Kerberos插件说明**   
&emsp;&emsp;Druid本身已经支持与Kerberos的集成,具体是通过自带的`Druid-Kerberos`插件实现的.关于该插件的说明可参考Druid官网的文档说明,此处给出直达[传送门](http://druid.io/docs/0.10.1/development/extensions-core/druid-kerberos.html)

## 二、环境说明及代码调整
1. **环境**  
&emsp;&emsp;开始集成前先说明下工作环境,以免出现版本错配等问题
    - Druid: 使用的是当前的最新版本,即`0.10.1`.分布式的安装三个节点上,使用Ambari管理
    - Kerberos: 使用的是`Kerbers5`协议
    - 操作系统: `CentOS 6.8`
2. **代码调整**  
&emsp;&emsp;在刚开始根据Druid官网给出的配置进行集成时,通过查看coordinator的后台日志,发现其一直无法与historical节点通信,抛出以下异常:
```java
Caused by: java.io.IOException: Login failure for druid/@SUGO.COM from keytab /opt/apps/kerberos/keytabs/druid.keytab
	at org.apache.hadoop.security.UserGroupInformation.loginUserFromKeytab(UserGroupInformation.java:890) ~[?:?]
	at io.druid.security.kerberos.DruidKerberosUtil.authenticateIfRequired(DruidKerberosUtil.java:115) ~[?:?]
	... 13 more
Caused by: javax.security.auth.login.LoginException: Unable to obtain password from user

	at com.sun.security.auth.module.Krb5LoginModule.promptForPass(Krb5LoginModule.java:897) ~[?:1.8.0_91]
	at com.sun.security.auth.module.Krb5LoginModule.attemptAuthentication(Krb5LoginModule.java:760) ~[?:1.8.0_91]
	at com.sun.security.auth.module.Krb5LoginModule.login(Krb5LoginModule.java:617) ~[?:1.8.0_91]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:1.8.0_91]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[?:1.8.0_91]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_91]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_91]
	at javax.security.auth.login.LoginContext.invoke(LoginContext.java:755) ~[?:1.8.0_91]
	at javax.security.auth.login.LoginContext.access$000(LoginContext.java:195) ~[?:1.8.0_91]
	at javax.security.auth.login.LoginContext$4.run(LoginContext.java:682) ~[?:1.8.0_91]
	at javax.security.auth.login.LoginContext$4.run(LoginContext.java:680) ~[?:1.8.0_91]
	at java.security.AccessController.doPrivileged(Native Method) ~[?:1.8.0_91]
	at javax.security.auth.login.LoginContext.invokePriv(LoginContext.java:680) ~[?:1.8.0_91]
	at javax.security.auth.login.LoginContext.login(LoginContext.java:587) ~[?:1.8.0_91]
	at org.apache.hadoop.security.UserGroupInformation.loginUserFromKeytab(UserGroupInformation.java:881) ~[?:?]
	at io.druid.security.kerberos.DruidKerberosUtil.authenticateIfRequired(DruidKerberosUtil.java:115) ~[?:?]

```
&emsp;&emsp;配置:
```
druid.hadoop.security.kerberos.principal=druid@SUGO.COM
druid.hadoop.security.spnego.principal=HTTP/_HOST@SUGO.COM
```
&emsp;&emsp;结合异常和配置,可以判断应该是因为keytab中没有包含`druid@SUGO.COM`的密码.那么按照官网给出的配置样例应该是想让我们创建一个`druid@SUGO.COM`公共用户供Druid节点内部交流使用,也就是在keytab中加入`druid@SUGO.COM`的密码.  
  
&emsp;&emsp;但是,再深入考虑一层,如果包含`druid@SUGO.COM`的keytab不小心被集群外的机器获取到,那么它可以利用这个`kinint -k -t { keytap path } druid@SUGO.COM`这个命令轻易侵入到Druid的内部交流中,这就与我们权限控制的初衷相违背了.所以我们参考第二个配置将`druid.hadoop.security.kerberos.principal=druid@SUGO.COM`改为`druid.hadoop.security.kerberos.principal=druid/_HOST@SUGO.COM`,也就是加上机器本身的hostname作为控制,以免上述情况的发生.

 &emsp;&emsp;相应的我们也就需要对解析这个参数的代码进行修改,具体的修改说明如下:

 - 在`io.druid.security.kerberos.DruidKerberosUtil`类中增加以下属性定义并初始化
```java
  private static String hostname;    

  static{ //利用静态代码快初始化hostname
    try {
      hostname = InetAddress.getLocalHost().getHostName();   
    } catch (UnknownHostException e) {
      log.error("err ocurr when get the hostname",e);
    }
  }
```
- 在`io.druid.security.kerberos.DruidKerberosUtil`类的`authenticateIfRequired`方法中增加一句代码
```java

  public static void authenticateIfRequired(AuthenticationKerberosConfig config)
    throws IOException
  {
    String principal = config.getPrincipal();
    principal = principal.replace("_HOST",hostname);  //增加此句代码,将"_HOST"占位符替换为真正的hostname
    String keytab = config.getKeytab();
    if (!Strings.isNullOrEmpty(principal) && !Strings.isNullOrEmpty(keytab)) {
      Configuration conf = new Configuration();
      conf.set(CommonConfigurationKeysPublic.HADOOP_SECURITY_AUTHENTICATION, "kerberos");
      UserGroupInformation.setConfiguration(conf);
      try {
        if (UserGroupInformation.getCurrentUser().hasKerberosCredentials() == false
            || !UserGroupInformation.getCurrentUser().getUserName().equals(principal)) {
          log.info("trying to authenticate user [%s] with keytab [%s]", principal, keytab);
          UserGroupInformation.loginUserFromKeytab(principal, keytab);
        }
      }
      catch (IOException e) {
        throw new ISE(e, "Failed to authenticate user principal [%s] with keytab [%s]", principal, keytab);
      }
    }
  }

```
&emsp;&emsp;最后将Druid代码重新编译打包,在集群中更新

## 三、集成Kerberos

1. **安装Kerberos**

- 配置Druid集群机器的的hosts文件,确保集群每台机器hostname配置一致   
```bash
$ cat /etc/hosts
127.0.0.1       localhost
192.168.0.223 dev223.sugo.net
192.168.0.224 dev224.sugo.net
192.168.0.225 dev225.sugo.net
```  
>注意：hostname请使用小写,要不然在集成Kerberos时会出现一些错误.


- 安装kdc服务   
    在Druid集群机器中选择一台机器作为kdc server,本例选择`dev224.sugo.net`.

```bash
$ yum install krb5-server krb5-libs krb5-auth-dialog krb5-workstation  -y
```

- 安装devel和workstation服务  
    在Druid集群的所有机器上安装devel和workstation服务
```bash
$ ssh dev223.sugo.net "yum install krb5-devel krb5-workstation -y"
$ ssh dev224.sugo.net "yum install krb5-devel krb5-workstation -y"
$ ssh dev225.sugo.net "yum install krb5-devel krb5-workstation -y"
```

- 修改配置文件   
> kdc 服务器涉及到三个配置文件：  
`/etc/krb5.conf`  
`/var/kerberos/krb5kdc/kdc.conf`   
`/var/kerberos/krb5kdc/kadm5.acl`

&emsp;**1.1**. 配置Kerberos的一种方法是编辑配置文件`/etc/krb5.conf`,默认安装的文件中包含多个示例项.

```bash
$ cat /etc/krb5.conf
[logging]
default = FILE:/var/log/krb5libs.log
kdc = FILE:/var/log/krb5kdc.log
admin_server = FILE:/var/log/kadmind.log

[libdefaults]
default_realm = SUGO.COM
dns_lookup_realm = false
dns_lookup_kdc = false
ticket_lifetime = 24h
renew_lifetime = 7d
forwardable = true
renewable = true
udp_preference_limit = 1
default_tgs_enctypes = arcfour-hmac
default_tkt_enctypes = arcfour-hmac

[realms]
SUGO.COM = {
kdc = dev224.sugo.net:88
admin_server = dev224.sugo.net:749
}

[domain_realm]
.sugo.com = SUGO.COM
sugo.com = SUGO.COM

[kdc]
profile=/var/kerberos/krb5kdc/kdc.conf
```

&emsp;配置说明:        
- `[logging]`: 表示 server 端的日志的打印位置
- `[libdefaults]`:每种连接的默认配置，需要注意以下几个关键的小配置:

    参数名 | 意义  
    ----|------
    `default_realm` | 设置 Kerberos 应用程序的默认领域。如果有多个领域，只需向 `[realms]` 节添加其他的语句
    `udp_preference_limit` | 设置禁止使用udp    
    `ticket_lifetime` | 表明凭证生效的时限，一般为24小时   
    `renew_lifetime` | 表明凭证最长可以被延期的时限，一般为一个礼拜.当凭证过期之后，对安全认证的服务的后续访问则会失败  

- `[realms]`：列举使用的 realm  

    参数名 | 意义  
    ----|------
    `kdc` | 表要 kdc 的位置,格式是 `机器:端口`
    `admin_server` | 代表 admin 的位置,格式是 `机器:端口`

> krb5.conf文件包含 Kerberos 的配置信息。例如,KDC 的位置，Kerbero 的 admin 和 realms 等.需要所有使用 Kerberos 的机器上的配置文件都同步.这里仅列举本例需要的基本配置。详细介绍参考:[krb5conf](http://web.mit.edu/~kerberos/krb5-devel/doc/admin/conf_files/krb5_conf.html)

&emsp;**1.2**. 修改kdc server(即`dev224.sugo.net`)的 `/var/kerberos/krb5kdc/kdc.conf`
```bash
$ cat /var/kerberos/krb5kdc/kdc.conf
[kdcdefaults]
 kdc_ports = 88
 kdc_tcp_ports = 88

[realms]
 SUGO.COM = {
  acl_file = /var/kerberos/krb5kdc/kadm5.acl
  dict_file = /usr/share/dict/words
  admin_keytab = /var/kerberos/krb5kdc/kadm5.keytab
  supported_enctypes = aes256-cts:normal aes128-cts:normal des3-hmac-sha1:normal arcfour-hmac:normal des-hmac-sha1:normal des-cbc-md5:normal des-cbc-crc:normal
 }
```

&emsp;配置说明: 

    参数名 | 意义  
    ----|------
    `SUGO.COM` | 设定的realm,名字随意. Kerberos可以支持多个 realm，会增加复杂度.大小写敏感，一般为了识别使用全部大写.这个 realm 跟机器的host没有大关系
    `acl_file` | 标注了 admin 的用户权限,需要用户自己创建. 文件格式是：`Kerberos_principal permissions [target_principal] [restrictions]`
    `dict_file` | 不允许设为密码的字符串集所在的文件位置
    `admin_keytab` | KDC 进行校验的 keytab
    `supported_enctypes` | 支持的校验方式
 
>关于AES-256加密：  
&emsp;&emsp;对于使用`centos5.6及以上的系统`,默认使用`aes256`来加密的. 而JAVA使用`aes256`验证方式需要安装额外的`Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy File`这个jar包,这就需要集群中的所有节点上安装JCE. 请根据本地环境的JDK版本下载相应的JCE资源.本例使用的是[JCE8](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html)  
&emsp;&emsp;下载的文件是一个 zip 包,解开后将里面的`local_policy.jar`和`US_export_policy.jar`两个文件放到下面的目录中：`$JAVA_HOME/jre/lib/security`,最后要注意文件的读权限

&emsp;**1.3**. 修改kdc server(即`dev224.sugo.net`)的 `/var/kerberos/krb5kdc/kadm5.acl`   
&emsp;&emsp;Kerberos使用`kadm5.acl`对自身创建的数据库进行权限控制, 同时也用它控制哪些主体可以操作其他主体,具体的配置可以参考[这里](http://web.mit.edu/~kerberos/krb5-devel/doc/admin/conf_files/kadm5_acl.html),本例的配置如下:
```bash
$ cat /var/kerberos/krb5kdc/kadm5.acl
*/admin@SUGO.COM	*
```
> `*` 代表赋予所有权限

&emsp;**1.4**. 同步配置文件  
&emsp;&emsp;将kdc中的`/etc/krb5.conf`拷贝到集群中其他服务器即可
```bash
$ scp /etc/krb5.conf dev223.sugo.net:/etc/krb5.conf
$ scp /etc/krb5.conf dev225.sugo.net:/etc/krb5.conf
```

&emsp;**1.5**. 创建数据库   
&emsp;&emsp;在 `dev224.sugo.net`上运行初始化数据库命令.其中 `-r` 指定对应 realm.
```bash
$ kdb5_util create -r SUGO.COM -s
```
&emsp;&emsp;该命令会在 `/var/kerberos/krb5kdc/` 目录下创建 principal 数据库,如果遇到数据库已经存在的提示，可以把`/var/kerberos/krb5kdc/`目录下的 principal 的相关文件都删除掉.默认的数据库名字都是 principal.可以使用`-d`指定数据库名字

&emsp;**1.6**. 启动服务并设置开机启动  
&emsp;&emsp;在 `dev224.sugo.net`上运行:
- 启动服务
```
$ service krb5kdc start
$ service kadmin start
```
- 设置开机启动
```
$ chkconfig krb5kdc on
$ chkconfig kadmin on
```
&emsp;**1.7**. 创建 kerberos 管理员  
&emsp;&emsp;在 `dev224.sugo.net`上使用 `kadmin.local` 命令创建管理员账户  

```
$ kadmin.local -q "addprinc root/admin"    //手动输入两次密码
```
>注意:这个命令要求有访问kdc服务器的 `root`权限

&emsp;**1.8**. 测试kerberos  
&emsp;&emsp;以下内容仅仅是为了测试，你可以直接跳到下部分内容,以下命令均在`dev224.sugo.net`上执行.

- 查看principals
```bash
$ kadmin.local -q "list_principals"
```
- 添加一个新的principal
```bash
$ kadmin.local -q "addprinc user1"
Authenticating as principal HTTP/admin@SUGO.COM with password.
WARNING: no policy specified for user1@SUGO.COM; defaulting to no policy
Enter password for principal "user1@SUGO.COM": 
Re-enter password for principal "user1@SUGO.COM": 
Principal "user1@SUGO.COM" created.
```
- 删除principal
```bash
kadmin.local -q "delprinc user1"
Authenticating as principal HTTP/admin@SUGO.COM with password.
Are you sure you want to delete the principal "user1@SUGO.COM"? (yes/no): yes
Principal "user1@SUGO.COM" deleted.
Make sure that you have removed this principal from all ACLs before reusing.
```

- ticket测试
> 用`kadmin.local -q "addprinc test"`创建一个测试用户,密码设为`test`
```bash
$ kinit test     //通过kinit命令获取test用户的ticket
Password for test@SUGO.COM:   //输入test用户密码
$ klist    //查看当前获取到的ticket
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: test@SUGO.COM

Valid starting       Expires              Service principal
2017-11-22T20:12:12  2017-11-23T20:12:12  krbtgt/SUGO.COM@SUGO.COM
	renew until 2017-11-22T20:12:12
$ kdestroy   //销毁该test用户的 ticket
```

2. **Druid集成Kerberos**

&emsp;**2.1**. 为Druid创建用户   
&emsp;&emsp;根据Druid官网给出的关于`Druid-Kerberos`插件配置:
```
druid.hadoop.security.kerberos.principal=druid/_HOST@SUGO.COM
druid.hadoop.security.spnego.principal=HTTP/_HOST@SUGO.COM
```
&emsp;&emsp;Druid内部交流需要用到`druid/_HOST@SUGO.COM`和`HTTP/_HOST@SUGO.COM`用户,因此我们需要为集群中的每台机器创建以上两个用户,在 `dev224.sugo.net`上执行以下命令:
```
kadmin.local -q "addprinc -randkey druid/dev223.sugo.net@SUGO.COM"
kadmin.local -q "addprinc -randkey druid/dev224.sugo.net@SUGO.COM"
kadmin.local -q "addprinc -randkey druid/dev225.sugo.net@SUGO.COM"
kadmin.local -q "addprinc -randkey HTTP/dev223.sugo.net@SUGO.COM"
kadmin.local -q "addprinc -randkey HTTP/dev224.sugo.net@SUGO.COM"
kadmin.local -q "addprinc -randkey HTTP/dev225.sugo.net@SUGO.COM"
```
> `-randkey`标志没有为新principal设置密码，而是指示kadmin生成一个随机密钥。之所以在这里使用这个标志，是因为以上principal不需要用户交互,仅是Druid内部使用,同时也可以隔绝其它principal的访问,保证了Druid内部的安全性.

&emsp;&emsp;创建完成后，查看：
```
$ kadmin.local -q "listprincs"
```

&emsp;**2.2**. 创建keytab文件   
&emsp;&emsp;keytab是包含principals和加密principal key的文件. keytab文件对于每个host是唯一的,因为key中包含 hostname. keytab文件用于**不需要人工交互**和保存纯文本密码,实现到kerberos上验证一个主机上的principal. 
因为服务器上可以访问keytab文件即可以以principal的身份通过kerberos的认证,所以,keytab文件应该被妥善保存,应该只有少数的用户可以访问.  
&emsp;&emsp;在` dev224.sugo.net `节点，即 KDC server 节点上执行下面命令为`druid/_HOST@SUGO.COM`和`HTTP/_HOST@SUGO.COM`用户创建keytab文件：
```bash
$ cd /var/kerberos/krb5kdc/
$ kadmin.local -q "xst  -k druid-unmerged.keytab  druid/dev223.sugo.net@SUGO.COM"
$ kadmin.local -q "xst  -k druid-unmerged.keytab  druid/dev224.sugo.net@SUGO.COM"
$ kadmin.local -q "xst  -k druid-unmerged.keytab  druid/dev225.sugo.net@SUGO.COM"

$ kadmin.local -q "xst  -k HTTP.keytab  HTTP/dev223.sugo.net@SUGO.COM"
$ kadmin.local -q "xst  -k HTTP.keytab  HTTP/dev224.sugo.net@SUGO.COM"
$ kadmin.local -q "xst  -k HTTP.keytab  HTTP/dev225.sugo.net@SUGO.COM"
```
&emsp;&emsp;这样,就会在`/var/kerberos/krb5kdc/`目录下生成`druid-unmerged.keytab`和`HTTP.keytab`两个文件，接下来使用`ktutil`合并这两个文件为 `druid.keytab`
```bash
$ cd /var/kerberos/krb5kdc/

$ ktutil
ktutil: rkt druid-unmerged.keytab
ktutil: rkt HTTP.keytab
ktutil: wkt druid.keytab
ktutil: exit
```
&emsp;&emsp;使用 klist 显示 druid.keytab 文件列表：
```
$ klist -ket  druid.keytab
Keytab name: FILE:druid.keytab
KVNO Timestamp         Principal
---- ----------------- --------------------------------------------------------
   2 11/16/17 09:41:21 druid/dev223.sugo.net@SUGO.COM (aes256-cts-hmac-sha1-96) 
   2 11/16/17 09:41:21 druid/dev223.sugo.net@SUGO.COM (aes128-cts-hmac-sha1-96) 
   2 11/16/17 09:41:21 druid/dev223.sugo.net@SUGO.COM (des3-cbc-sha1) 
   2 11/16/17 09:41:21 druid/dev223.sugo.net@SUGO.COM (arcfour-hmac) 
   2 11/16/17 09:41:22 druid/dev223.sugo.net@SUGO.COM (des-hmac-sha1) 
   2 11/16/17 09:41:22 druid/dev223.sugo.net@SUGO.COM (des-cbc-md5) 
   2 11/16/17 09:41:22 druid/dev224.sugo.net@SUGO.COM (aes256-cts-hmac-sha1-96) 
   2 11/16/17 09:41:22 druid/dev224.sugo.net@SUGO.COM (aes128-cts-hmac-sha1-96) 
   2 11/16/17 09:41:22 druid/dev224.sugo.net@SUGO.COM (des3-cbc-sha1) 
   2 11/16/17 09:41:22 druid/dev224.sugo.net@SUGO.COM (arcfour-hmac) 
   2 11/16/17 09:41:22 druid/dev224.sugo.net@SUGO.COM (des-hmac-sha1) 
   2 11/16/17 09:41:22 druid/dev224.sugo.net@SUGO.COM (des-cbc-md5) 
   2 11/16/17 09:41:22 druid/dev225.sugo.net@SUGO.COM (aes256-cts-hmac-sha1-96) 
   2 11/16/17 09:41:22 druid/dev225.sugo.net@SUGO.COM (aes128-cts-hmac-sha1-96) 
   2 11/16/17 09:41:22 druid/dev225.sugo.net@SUGO.COM (des3-cbc-sha1) 
   2 11/16/17 09:41:22 druid/dev225.sugo.net@SUGO.COM (arcfour-hmac) 
   2 11/16/17 09:41:22 druid/dev225.sugo.net@SUGO.COM (des-hmac-sha1) 
   2 11/16/17 09:41:22 druid/dev225.sugo.net@SUGO.COM (des-cbc-md5) 
   2 11/16/17 09:41:22 HTTP/dev223.sugo.net@SUGO.COM (aes256-cts-hmac-sha1-96) 
   2 11/16/17 09:41:22 HTTP/dev223.sugo.net@SUGO.COM (aes128-cts-hmac-sha1-96) 
   2 11/16/17 09:41:22 HTTP/dev223.sugo.net@SUGO.COM (des3-cbc-sha1) 
   2 11/16/17 09:41:22 HTTP/dev223.sugo.net@SUGO.COM (arcfour-hmac) 
   2 11/16/17 09:41:22 HTTP/dev223.sugo.net@SUGO.COM (des-hmac-sha1) 
   2 11/16/17 09:41:23 HTTP/dev223.sugo.net@SUGO.COM (des-cbc-md5) 
   2 11/16/17 09:41:23 HTTP/dev224.sugo.net@SUGO.COM (aes256-cts-hmac-sha1-96) 
   2 11/16/17 09:41:23 HTTP/dev224.sugo.net@SUGO.COM (aes128-cts-hmac-sha1-96) 
   2 11/16/17 09:41:23 HTTP/dev224.sugo.net@SUGO.COM (des3-cbc-sha1) 
   2 11/16/17 09:41:23 HTTP/dev224.sugo.net@SUGO.COM (arcfour-hmac) 
   2 11/16/17 09:41:23 HTTP/dev224.sugo.net@SUGO.COM (des-hmac-sha1) 
   2 11/16/17 09:41:23 HTTP/dev224.sugo.net@SUGO.COM (des-cbc-md5) 
   2 11/16/17 09:41:23 HTTP/dev225.sugo.net@SUGO.COM (aes256-cts-hmac-sha1-96) 
   2 11/16/17 09:41:23 HTTP/dev225.sugo.net@SUGO.COM (aes128-cts-hmac-sha1-96) 
   2 11/16/17 09:41:23 HTTP/dev225.sugo.net@SUGO.COM (des3-cbc-sha1) 
   2 11/16/17 09:41:23 HTTP/dev225.sugo.net@SUGO.COM (arcfour-hmac) 
   2 11/16/17 09:41:23 HTTP/dev225.sugo.net@SUGO.COM (des-hmac-sha1) 
   2 11/16/17 09:41:23 HTTP/dev225.sugo.net@SUGO.COM (des-cbc-md5) 
```

&emsp;&emsp;验证是否正确合并了key,使用合并后的keytab,分别使用druid和HTTP principals来获取证书.
```
$ kinit -k -t druid.keytab druid/dev224.sugo.net@SUGO.COM
$ kinit -k -t druid.keytab HTTP/dev224.sugo.net@SUGO.COM
```
&emsp;&emsp; 如果出现错误：`kinit: Key table entry not found while getting initial credentials`,
则上面的合并有问题，重新执行前面的操作

&emsp;**2.3**. 部署kerberos keytab文件  
&emsp;&emsp;拷贝`dev224.sugo.net`机器上的druid.keytab 文件到其他节点的 /opt/apps/kerberos/keytabs 目录
```
$ cd /var/kerberos/krb5kdc/

$ scp druid.keytab dev223.sugo.net:/opt/apps/kerberos/keytabs
$ scp druid.keytab dev225.sugo.net:/opt/apps/kerberos/keytabs
```

&emsp;&emsp;设置权限,分别在 `dev223.sugo.net`、`dev224.sugo.net`、`dev225.sugo.net` 上执行：
```
$ chown druid:druid /opt/apps/kerberos/keytabs/hdfs.keytab
$ chmod /opt/apps/kerberos/keytabs/hdfs.keytab
```
&emsp;&emsp;由于拥有keytab相当于有了永久凭证,不需要提供密码(如果修改kdc中的principal的密码,则该keytab就会失效),所以其他用户如果对该文件有读权限，就可以冒充keytab中指定的用户身份访问druid,所以 keytab文件需要确保只对 owner 有读权限(0400)

&emsp;**2.4**. 修改 Druid 配置文件  
&emsp;&emsp;修改Druid项目中`distribution`目录下的`pom`文件,在`project.build.plugins.plugin.executions.execution..configuration.arguments` 标签加入以下两行代码:
```
<argument>-c</argument>
<argument>io.druid.extensions:druid-kerberos</argument>
```
&emsp;&emsp;在Druid的公共配置里,增加kerberos相关的配置:
```
druid.extensions.loadList
=["postgresql-metadata-storage", "druid-hdfs-storage", "druid-lucene-extensions", "druid-kerberos"]     //增加druid-kerberos插件
druid.hadoop.security.kerberos.keytab=/opt/apps/kerberos/keytabs/druid.keytab
druid.hadoop.security.kerberos.principal=druid/_HOST@SUGO.COM
druid.hadoop.security.spnego.keytab=/opt/apps/kerberos/keytabs/druid.keytab
druid.hadoop.security.spnego.principal=HTTP/_HOST@SUGO.COM
```

&emsp;&emsp;将Druid项目重新编译打包,在集群中更新启动.在集群机器中查看各个服务的后台日志,若没有出现错误则说明集成成功.

# 外部访问
&emsp;&emsp;外部访问主要是指集群外部机器通过`命令行、浏览器、程序`等方式访问Druid的相关资源.

## 三、Linux 访问
>本例以`Ubuntu 16.04.2 LTS`作为例子讲述如何在Liunx下访问Druid资源   

1. **注册用户**     
&emsp;&emsp;在kdc,即`dev224.sugo.net`上为要进行的外部客户端注册一个`principal`:
```bash
$ kadmin.local -q "addprinc cyz@SUGO.COM"
Authenticating as principal HTTP/admin@SUGO.COM with password.
WARNING: no policy specified for cyz@SUGO.COM; defaulting to no policy
Enter password for principal "cyz@SUGO.COM":   
Re-enter password for principal "cyz@SUGO.COM": 
Principal "cyz@SUGO.COM" created.
```

2. **生成keytab文件**   
&emsp;&emsp;在kdc上执行命令
```bash
$ cd /var/kerberos/krb5kdc/
$ kadmin.local -q "xst  -k cyz.keytab  cyz@SUGO.COM"
```

3. **安装kerberos客户端**   
&emsp;&emsp;在要访问的外部客户端命令行中输入以下命令:
```bash
$ sudo apt-get install krb5-user
```
> 在安装过程中可能会跳出些弹框,这是用于配置外部客户端的`krb5.conf`,这里不了解的话可以随意填一些东西以便安装继续进行,后面会有相应的操作覆盖这个`krb5.conf`

4. **获取keytab和配置文件**  
- 将第2步在kdc上生成的`cyz.keytab`文件复制到外部客户端的`/opt/apps/kerberos/keytabs`目录
```bash
$ cd /var/kerberos/krb5kdc/
$ scp cyz.keytab root@{Client IP}:/opt/apps/kerberos/keytabs

```
- 将kdc上的`/etc/krb5.conf`文件复制到外部客户端的`/etc`目录下:
```bash
$ scp /etc/krb5.conf root@{Client IP}:/etc
```

5. **修改外部客户端的hosts文件**  
&emsp;&emsp;在要访问的外部客户端中加入druid集群的域名解析:
```bash
$ cat /etc/hosts
127.0.0.1	localhost

192.168.0.225 dev225.sugo.net
192.168.0.223 dev223.sugo.net
192.168.0.224 dev224.sugo.net
```
6. **获取ticket**  
&emsp;&emsp;在要访问的外部客户端中利用`kinit`命令获取ticket,输入`klist`命令查看ticket:
```bash
$ kinit -k -t  ../kerberos/keytabs/cyz.keytab cyz@SUGO.COM
$ klist
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: cyz@SUGO.COM

Valid starting       Expires              Service principal
2017-11-28T11:23:18  2017-11-29T11:23:18  krbtgt/SUGO.COM@SUGO.COM
	renew until 2017-11-28T11:23:18
```

7. **通过命令行访问druid接口**  
&emsp;&emsp;通过`curl`命令的`--negotiate`参数,在外部客户端的命令行访问druid
```bash
$ curl --negotiate -u:cyz -X 'POST' -H 'Content-Type:application/json' -d @task-spec.json http://dev225.sugo.net:8090/druid/indexer/v1/task
```

8. **通过浏览器访问druid接口**  
&emsp;&emsp;经过多番测试,目前支持Firefox浏览器的访问,不支持Chrome浏览器   

- 在外部客户端打开Firefox浏览器,在地址栏输入`about:config`进入配置页面,搜索`network.negotiate-auth.trusted-uris`项

- 双击,输入`"http://druid-coordinator-hostname:ui-port"` 和` "http://druid-overlord-hostname:port"`, 以`,`隔开:
```
http://dev225.sugo.net:8090,http://dev225.sugo.net:8081
```
&emsp;&emsp;在新开页面的地址栏输入`http://dev225.sugo.net:8090`和`http://dev225.sugo.net:8081`访问druid资源

## 四、Windows 访问
>本例以`Win7`操作系统作为例子讲述如何在Windows下访问Druid资源 

1. **注册用户、获取keytab文件**   
&emsp;&emsp;参考`Linux 访问`中的第1、2步,在kdc上进行用户注册、生成keytab文件操作,并将keytab文件放置在客户端的`C:\ProgramData\Kerberos\keytabs`目录下,方便起见,本例依旧使用`cyz@SUGO.COM`用户和`cyz.keytab`文件

2. **修改外部客户端的hosts文件**   
&emsp;&emsp;在客户端中修改`C:\Windows\System32\drivers\etc`目录下的`hosts`文件,在文件中加入druid集群的域名解析
```
192.168.0.225 dev225.sugo.net
192.168.0.223 dev223.sugo.net
192.168.0.224 dev224.sugo.net
```
>注意操作系统可能会提示`hosts`文件不可编辑,此时可以复制该文件内容并在其它目录另建一个`hosts`文件后粘贴,然后追加以上内容,把新的`hosts`文件复制到`C:\Windows\System32\drivers\etc`覆盖原来的`hosts`文件

3. **安装Kerberos客户端**  
&emsp;&emsp;在[MIT Kerberos下载页面](http://web.mit.edu/kerberos/dist/index.html)中根据自身的操作系统位数下载对应的安装包, 本例客户端下载的是[kfw-4.1-amd64.msi](http://web.mit.edu/kerberos/dist/kfw/4.1/kfw-4.1-amd64.msi).下载后直接双击,不用特意配置即可自动化安装完毕 

4. **修改外部客户端的Kerberos配置**
- 修改外部客户端的`krb5.ini`文件  
&emsp;&emsp;Windows系统下的Kerberos配置文件是`krb5.ini`,不是`krb5.conf`, 在第三步的客户端安装完毕之后,该文件默认位于`C:\ProgramData\MIT\Kerberos5`目录下, 如果没有找到,可以在windows文件浏览器中搜索`krb5.ini`. 将kdc上的`krb5.conf`文件内容复制、覆盖到`krb5.ini`文件

- 修改客户端的path变量  
&emsp;&emsp;在第三步的客户端安装完毕之后,它就自动的在 PATH 里面加上了自己的执行目录,默认是`C:\Program Files\MIT\Kerberos\bin`. 但是,有些机器本身安装的某些jdk里面也带了一些`kinit, ktab, klist`等软件,所以输入`kinit 、klist`的时候执行的可能是jdk里面的工具或者是`C:\Windows\System32`路径下的命令, 因此,应把kfw的path放在最前面,然后重启客户端.

5. **获取ticket**  
&emsp;&emsp;在客户端的dos窗口中利用`kinit`命令获取ticket,输入`klist`命令查看ticket:
```
C:\Users>kinit -k -t C:\ProgramData\Kerberos\keytabs cyz@SUGO.COM
C:\Users>klist 
Ticket cache: API:Initial default ccache
Default principal: cyz@SUGO.COM

Valid starting       Expires              Service principal
11/28/17 14:38:40  11/29/17 14:38:40  krbtgt/SUGO.COM@SUGO.COM
	renew until 11/28/17 14:38:40
```
>也可以打开桌面的`MIT Kerberos Ticket Manager`快捷方式,点击`Get Ticket`按钮，在弹出窗口中手动输入`cyz@SUGO.COM`和对应密码获取`ticket`

6. **通过浏览器访问druid接口**  
&emsp;&emsp;在Windows系统下目前仅支持Firefox浏览器的访问,不支持命令行的`curl`命令以及Chrome浏览器的访问

- 在外部客户端打开Firefox浏览器,在地址栏输入`about:config`进入配置页面

- 在配置页面中逐个搜索配置以下属性:
```
network.negotiate-auth.trusted-uris = http://dev225.sugo.net:8090,http://dev225.sugo.net:8081
network.negotiate-auth.using-native-gsslib = false
network.negotiate-auth.gsslib = C:\Program Files\MIT\Kerberos\bin\gssapi32.dll
network.auth.use-sspi = false
network.negotiate-auth.allow-non-fqdn = true
```

&emsp;&emsp;在新开页面的地址栏输入`http://dev225.sugo.net:8090`和`http://dev225.sugo.net:8081`访问druid资源,如果没能成功访问,尝试重启Firefox浏览器再进行访问

## 五、程序访问
>本例以Linux系统下使用`HttpClient`作为例子讲述如何编写程序访问Druid资源   

1. **注册用户、获取keytab文件**   
&emsp;&emsp;参考`Linux 访问`中的第1、2步,在kdc上进行用户注册、生成keytab文件操作,并将keytab文件放置在客户端的`/opt/apps/kerberos/keytabs`目录下,方便起见本例依旧使用`cyz@SUGO.COM`用户和`cyz.keytab`文件

2. **获取krb5.conf**  
&emsp;&emsp;将kdc上的`/etc/krb5.conf`文件复制到外部客户端的`/etc`目录下:
```bash
$ scp /etc/krb5.conf root@{Client IP}:/etc
```

3. **修改客户端的hosts文件**  
&emsp;&emsp;参考`Linux 访问`中的第5步,在客户端中加入druid集群的域名解析

4. **编写访问程序**

- 新建maven工程,pom文件依赖如下
```xml
  <properties>
        <httpclient.version>4.3.3</httpclient.version>
        <httpcore.version>4.3.3</httpcore.version>
    </properties>

    <dependencies>
        <!--httpclient-->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>${httpclient.version}</version>
        </dependency>
        <!-- httpcore -->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpcore</artifactId>
            <version>${httpcore.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-io</artifactId>
            <version>1.3.2</version>
        </dependency>
    </dependencies>
```

- 编写访问程序
```java
import org.apache.commons.io.IOUtils;
import org.apache.http.HttpResponse;
import org.apache.http.auth.AuthSchemeProvider;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.Credentials;
import org.apache.http.client.HttpClient;
import org.apache.http.client.config.AuthSchemes;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.methods.HttpUriRequest;
import org.apache.http.config.Lookup;
import org.apache.http.config.RegistryBuilder;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.auth.SPNegoSchemeFactory;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;

import javax.security.auth.Subject;
import javax.security.auth.kerberos.KerberosPrincipal;
import javax.security.auth.login.AppConfigurationEntry;
import javax.security.auth.login.Configuration;
import javax.security.auth.login.LoginContext;
import java.io.IOException;
import java.security.Principal;
import java.security.PrivilegedAction;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Set;

public class HttpClientWithAuth {

	public static String user ="cyz@SUGO.COM";
	public static String keytab="/opt/apps/kerberos/keytabs/cyz.keytab";
	public static String krb5Location="/etc/krb5.conf";
	public static String isDebugStr = "false";
	private String principal ;
	private String keyTabLocation ;

	public HttpClientWithAuth(String principal, String keyTabLocation, String krb5Location) {
		this.principal = principal;
		this.keyTabLocation = keyTabLocation;
		System.setProperty("java.security.krb5.conf", krb5Location);
		System.setProperty("sun.security.spnego.debug", isDebugStr);  //
		System.setProperty("sun.security.krb5.debug", isDebugStr);
	}

	//模拟curl命令使用kerberos认证
	private static HttpClient buildSpengoHttpClient() {
		HttpClientBuilder builder = HttpClientBuilder.create();
		Lookup<AuthSchemeProvider> authSchemeRegistry = RegistryBuilder.<AuthSchemeProvider>create().
				register(AuthSchemes.SPNEGO, new SPNegoSchemeFactory(true)).build();   //采用 SPNEGO 认证方案
			builder.setDefaultAuthSchemeRegistry(authSchemeRegistry);
		BasicCredentialsProvider credentialsProvider = new BasicCredentialsProvider();      //kerberos的ticket提供者
		credentialsProvider.setCredentials(new AuthScope(null, -1, null), new Credentials() {
			@Override
			public Principal getUserPrincipal() {
				return null;
			}
			@Override
			public String getPassword() {
				return null;
			}
		});
		builder.setDefaultCredentialsProvider(credentialsProvider);
		CloseableHttpClient httpClient = builder.build();
		return httpClient;
	}

	//配置kerberos属性
	private  Configuration getKerberosConfig(){
		return new Configuration() {
			@SuppressWarnings("serial")
			@Override
			public AppConfigurationEntry[] getAppConfigurationEntry(String name) {
				return new AppConfigurationEntry[] { new AppConfigurationEntry("com.sun.security.auth.module.Krb5LoginModule",
						AppConfigurationEntry.LoginModuleControlFlag.REQUIRED, new HashMap<String, Object>() {
					{
						put("useTicketCache", "false");
						put("useKeyTab", "true");
						put("keyTab", keyTabLocation);
						//Krb5 in GSS API needs to be refreshed so it does not throw the error
						//Specified version of key is not available
						put("refreshKrb5Config", "true");
						put("principal", principal);
						put("storeKey", "true");
						put("doNotPrompt", "true");
						put("isInitiator", "true");
						put("debug", isDebugStr);
					}
				}) };
			}
		};
	}

	//Http Get Method
	public HttpResponse callRestUrl(final String url,final String userId) {
		System.out.println(String.format("Calling KerberosHttpClient %s %s %s",this.principal, this.keyTabLocation, url));
		Set<Principal> princ = new HashSet<Principal>(1);
		princ.add(new KerberosPrincipal(userId));
		Subject sub = new Subject(false, princ, new HashSet<Object>(), new HashSet<Object>());
		try {
			//认证模块：Krb5Login
			LoginContext lc = new LoginContext("Krb5Login", sub, null, getKerberosConfig());
			lc.login();
			Subject serviceSubject = lc.getSubject();
			return Subject.doAs(serviceSubject, new PrivilegedAction<HttpResponse>() {
				HttpResponse httpResponse = null;
				@Override
				public HttpResponse run() {
					try {
						HttpUriRequest request = new HttpGet(url);

						HttpClient spnegoHttpClient = buildSpengoHttpClient();
						httpResponse = spnegoHttpClient.execute(request);
						return httpResponse;
					} catch (IOException ioe) {
						ioe.printStackTrace();
					}
					return httpResponse;
				}
			});
		} catch (Exception le) {
			le.printStackTrace();
		}
		return null;
	}

	//Http Post Method
	public HttpResponse postRestUrl(final String url,final String userId,final String params) {
		System.out.println(String.format("Calling KerberosHttpClient %s %s %s",this.principal, this.keyTabLocation, url));
		Set<Principal> princ = new HashSet<Principal>(1);
		princ.add(new KerberosPrincipal(userId));
		Subject sub = new Subject(false, princ, new HashSet<Object>(), new HashSet<Object>());
		try {
			//认证模块：Krb5Login
			LoginContext lc = new LoginContext("Krb5Login", sub, null, getKerberosConfig());
			lc.login();
			Subject serviceSubject = lc.getSubject();
			return Subject.doAs(serviceSubject, new PrivilegedAction<HttpResponse>() {
				HttpResponse httpResponse = null;
				@Override
				public HttpResponse run() {
					try {
						StringEntity entity = new StringEntity(params,"UTF-8");
						entity.setContentEncoding("UTF-8");
						entity.setContentType("application/json");
						HttpPost httpPost = new HttpPost(url);
						httpPost.setEntity(entity);

						HttpClient spnegoHttpClient = buildSpengoHttpClient();
						httpResponse = spnegoHttpClient.execute(httpPost);
						return httpResponse;
					} catch (IOException ioe) {
						ioe.printStackTrace();
					}
					return httpResponse;
				}
			});
		} catch (Exception le) {
			le.printStackTrace();
		}
		return null;
	}

	public static void main(String[] args) throws UnsupportedOperationException, IOException {

		HttpClientWithAuth restTest = new HttpClientWithAuth(user,keytab,krb5Location);

		String supervisorUrl = "http://dev225.sugo.net:8090/druid/indexer/v1/supervisor";

		String params = "{\n" +
				"  \"queryType\":\"lucene_timeBoundary\",\n" +
				"  \"dataSource\":\"druid-test002\",\n" +
				"  \"context\":null,\n" +
				"  \"intervals\":\"1000/3000\",\n" +
				"  \"bound\":null\n" +
				"}";
		String brokerUrl = "http://dev225.sugo.net:8082/druid/v2?pretty";

		HttpResponse response = restTest.postRestUrl(brokerUrl,user,params);  //查询数据源的maxTime,minTime
		printResponse(response);

		response = restTest.callRestUrl(supervisorUrl,user);    //获取正在运行的supervisor
		printResponse(response);
	}

	public static void printResponse(HttpResponse response) throws IOException {
		System.out.println("Status code " + response.getStatusLine().getStatusCode());
		System.out.println("返回值：\n"+new String(IOUtils.toByteArray(response.getEntity().getContent()), "UTF-8"));
	}
}
```

程序运行结果:

```
Calling KerberosHttpClient cyz@SUGO.COM /opt/apps/kerberos/keytabs/cyz.keytab http://dev225.sugo.net:8082/druid/v2?pretty
Status code 200
返回值：
[ {
  "timestamp" : "2017-05-01T00:00:01.520Z",
  "result" : {
    "maxTime" : "2017-05-30T23:59:58.141Z",
    "minTime" : "2017-05-01T00:00:01.520Z"
  }
} ]
Calling KerberosHttpClient cyz@SUGO.COM /opt/apps/kerberos/keytabs/cyz.keytab http://dev225.sugo.net:8090/druid/indexer/v1/supervisor
Status code 200
返回值：
["test-cyz"]
```

## 六、总结
&emsp;&emsp;本文介绍了 druid 集成 kerberos 认证的过程，其中主要需要注意以下几点：  
1. 配置 hosts,hostname 请使用小写.
2. 确保 kerberos 客户端和服务端连通
3. 替换 JRE 自带的 JCE jar 包
4. 设置keytab的权限
5. 启动服务前，先获取 ticket 再运行相关命令


- 参考资料  
1. [kerberos认证原理](http://blog.csdn.net/wulantian/article/details/42418231)
2. [kerberos官网](http://kerberos.org/)
3. [druid-kerberos插件配置说明](http://druid.io/docs/0.10.1/development/extensions-core/druid-kerberos.html)
4. [http 认证原理](http://free0007.iteye.com/blog/2012311)
5. [windows 下配置浏览器使用 kerberos](https://www.cnblogs.com/kischn/p/7443343.html)
6. [kerberos MIT实现](http://web.mit.edu/kerberos/dist/index.html)
7. [windows下用kerberos客户端和火狐登录HDP域](https://imaidata.github.io/blog/2017/06/27/windows%E4%B8%8B%E7%94%A8kerberos%E5%AE%A2%E6%88%B7%E7%AB%AF%E5%92%8C%E7%81%AB%E7%8B%90%E7%99%BB%E5%BD%95HDP%E5%9F%9F/)
8. [HDFS配置Kerberos认证](http://note.youdao.com/publicshare/?id=01ad217858be41d9c02c751c0111122f&type=note#/)
