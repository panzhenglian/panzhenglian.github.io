---
title: centos个人应用清单
date: 2016-05-28 10:57:12
tags:
---

### 首先来个清单列表

 - Shadowsocks	:服务器架设在国外，作为代理使用
 - JDK			
 - Apache 		:Apache 服务器，占用80端口，作为端口映射以及php程序的容器
 - tomcat		
 - mysql
 - php
 - phpMyAdmin	:很不错的数据库web管理工具，由php开发，配置简单


### Shadowsocks配置
  已经有一篇文章单独描述了，点击[这里](./2016/05/28/shadowsocks服务器/)
### JDK
  找到jdk官网，下载rpm包到服务器，执行

  ```base
  rpm -ivh jdk-7-linux-x64.rpm
  ```
  JDK默认安装在/usr/java中。
  #### 验证安装
  执行以下操作，查看信息是否正常：
  
  ```base
  [root@keke java]# java
  [root@keke java]# javac
  [root@keke java]# java -version
  java version "1.8.0_91"
  Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
  Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)
  ```
  #### 配置环境变量

  通过rpm安装包的方式安装后是不需要配置环境变量的，但是通过压缩文件解压需要配置jdk路径
  如下：
  ```
  vi + /etc/profile
  ```
  添加以下内容
  ```
  JAVA_HOME=//usr/java/jdk1.8.0_91
  JRE_HOME=//usr/java/jdk1.8.0_91/jre
  PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
  CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
  export JAVA_HOME JRE_HOME PATH CLASSPATH
  ```
使修改生效
```
source /etc/profile
```

### Apache、mysql、php
#### 配置防火墙，开启80端口、3306端口
```
vi /etc/sysconfig/iptables
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
```
重启防火墙使配置生效
```
/etc/init.d/iptables restart 
```

#### 关闭SELINUX,
```
vi /etc/selinux/config
#SELINUX=enforcing #注释掉
#SELINUXTYPE=targeted #注释掉
SELINUX=disabled #增加
:wq #保存，关闭
shutdown -r now #重启系统
```

#### 安装Apache
```
yum -y install httpd
```
配置
```
vi /etc/httpd/conf/httpd.conf #编辑
找到 #ServerName www.example.com:80
修改为 ServerName www.osyunwei.com:80 
#这里设置为你自己的域名，如果没有域名，可以设置为localhost
:wq #保存退出
```

```
chkconfig httpd on #设为开机启动
/etc/init.d/httpd restart #重启Apache
/etc/init.d/httpd start #启动Apache
```
或
```
service httpd start
service httpd restart
service httpd stop
```
centos 7 上命令有些许不同，应该是
```
systemctl httpd start
systemctl httpd restart
systemctl httpd stop
```
>apache默认的程序目录是/var/www/html

#### Apache 配置多域名端口转发

一台主机中可能运行着很多web应用，例如 gitlab，javaweb ，php
如果开启多个端口，看着端口怎么都别扭，而且不好记，于是，通过apache完成不同域名转发
编辑httpd.conf 
```
<VirtualHost *:80>
</VirtualHost>

<VirtualHost *:80>
    ServerName tomcat.panzhenglian.com
    ServerAlias tomcat.uuk4.com
    ProxyPass / http://localhost:7896/
    ProxyPassReverse / http://localhost:7896/
    ErrorLog logs/tomcat.uuk4.log
    CustomLog logs/tomcat.uuk4-access_log common
</VirtualHost>
```
> 第一行为默认配置，如果不配置，所有的请求都会跳转到最后一条 记录，
> tomcat 开启的端口是 7896，如果通过域名 tomcat.panzhenglian.com 进入的请求会转发至tomcat处理，
> 这样，一台主机上就可以在根目录部署多个网站了，

#### apache 安全
程序根目录添加 .htaccess 文件
配置：
```
<DirectoryMatch .*\.svn|.git|_svn/.*>
RewriteEngine On
RewriteRule ^(.*)(\.svn|.git|_svn)(/.*)$ /index.php [R=301]
</DirectoryMatch>
```
> 避免程序下的特定目录被外界访问，无需重启服务器，保存立即生效
> 笔者是通过 git webhooks 来完成程序部署的，因此程序目录下会有.git文件夹，




#### 安装MySQL
```
yum install mysql mysql-server 
/etc/init.d/mysqld start #启动MySQL
chkconfig mysqld on #设为开机启动
```

为root账户设置密码
```
mysql_secure_installation
回车，根据提示输入Y
输入2次密码，回车
根据提示一路输入Y
最后出现：Thanks for using MySQL!
MySql密码设置完成，重新启动 MySQL：
/etc/init.d/mysqld restart #重启
/etc/init.d/mysqld stop #停止
/etc/init.d/mysqld start #启动
```
mysql 启动等命令：
```
service mysqld start/restart/stop
# centos 7 # systemctl mysqld start/restart/stop
```
#### 安装PHP5
```
yum install php-fpm php-common php-devel php-mysqlnd php-mbstring php-mcrypt
```

#### 升级mysql 到 5.5，
>yum中的mysql版本较低，添加repo

```
rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm  
rpm -Uvh http://mirrors.neusoft.edu.cn/epel/6/i386/epel-release-6-8.noarch.rpm  
rpm -Uvh http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm  
rpm -Uvh http://dl.iuscommunity.org/pub/ius/stable/Redhat/6/x86_64/epel-release-6-5.noarch.rpm  
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm  
```
安装
```
yum --enablerepo=remi,remi-test install mysql mysql-server 
```
查看版本
```
mysql> select version();
+-----------+
| version() |
+-----------+
| 5.5.49    |
+-----------+
1 row in set (0.00 sec)
mysql>
```
#### 升级php到5.5版本

添加 epel 源,remi 源
```
# rpm -Uvh http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
# rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
```
安装 PHP
```
# yum --enablerepo=remi,remi-php55 install php-fpm php-common php-devel php-mysqlnd php-mbstring php-mcrypt
```

查看php版本
```
[root@keke /]# php -v
PHP 5.5.36 (cli) (built: May 25 2016 16:10:36)
Copyright (c) 1997-2015 The PHP Group
Zend Engine v2.5.0, Copyright (c) 1998-2015 Zend Technologies
[root@keke /]#
```
启动 php-fpm
```
# service php-fpm start
```

> 需要先安装php，再添加repo升级，不然可能apache服务器不执行php脚本
















