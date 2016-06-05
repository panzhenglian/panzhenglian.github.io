---
title: centos7 mysql数据库安装和配置(转载)
date: 2016-05-22 11:48:58
tags: 
 - Linux 
 - mysql
---
>原文链接[http://www.cnblogs.com/starof/p/4680083.html](http://www.cnblogs.com/starof/p/4680083.html)

## 一、系统环境
centos 7  
```base
[root@yl-web ~]# cat /etc/redhat-release
CentOS Linux release 7.2.1511 (Core)
```

## 二、mysql安装
一般网上给出的资料都是

```base
#yum install mysql
#yum install mysql-server
#yum install mysql-devel
```
安装mysql和mysql-devel都成功，但是安装mysql-server失败，如下：

```base
[root@yl-web yl]# yum install mysql-server
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.sina.cn
 * extras: mirrors.sina.cn
 * updates: mirrors.sina.cn
No package mysql-server available.
Error: Nothing to do
```
查资料发现是CentOS 7 版本将MySQL数据库软件从默认的程序列表中移除，用mariadb代替了。

有两种解决办法：

### 1、方法一：安装mariadb
MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可。开发这个分支的原因之一是：甲骨文公司收购了MySQL后，有将MySQL闭源的潜在风险，因此社区采用分支的方式来避开这个风险。MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。

安装mariadb，大小59 M。

```base
[root@yl-web yl]# yum install mariadb-server mariadb 
```
mariadb数据库的相关命令是：

systemctl start mariadb  #启动MariaDB

systemctl stop mariadb  #停止MariaDB

systemctl restart mariadb  #重启MariaDB

systemctl enable mariadb  #设置开机启动

所以先启动数据库

```base
[root@yl-web yl]# systemctl start mariadb
```
然后就可以正常使用mysql了

```base
[root@yl-web yl]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 5.5.41-MariaDB MariaDB Server

Copyright (c) 2000, 2014, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)

MariaDB [(none)]> 
复制代码
```

安装mariadb后显示的也是 MariaDB [(none)]> ，可能看起来有点不习惯。下面是第二种方法。

### 2、方法二：官网下载安装mysql-server
```base
# wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
# rpm -ivh mysql-community-release-el7-5.noarch.rpm
# yum install mysql-community-server
```
安装成功后重启mysql服务。

```base
# service mysqld restart
```
初次安装mysql，root账户没有密码。

```
[root@yl-web yl]# mysql -u root 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.6.26 MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.01 sec)

mysql> 
```
设置密码

```base
mysql> set password for 'root'@'localhost' =password('password');
Query OK, 0 rows affected (0.00 sec)

mysql> 
```
不需要重启数据库即可生效。

在mysql安装过程中如下内容：

```base
Installed:
  mysql-community-client.x86_64 0:5.6.26-2.el7
  mysql-community-devel.x86_64 0:5.6.26-2.el7
  mysql-community-libs.x86_64 0:5.6.26-2.el7
  mysql-community-server.x86_64 0:5.6.26-2.el7

Dependency Installed:
  mysql-community-common.x86_64 0:5.6.26-2.el7
  
Replaced:
  mariadb.x86_64 1:5.5.41-2.el7_0
  mariadb-devel.x86_64 1:5.5.41-2.el7_0
  mariadb-libs.x86_64 1:5.5.41-2.el7_0
  mariadb-server.x86_64 1:5.5.41-2.el7_0
```
所以安装完以后mariadb自动就被替换了，将不再生效。
```base
[root@yl-web yl]# rpm -qa |grep mariadb
[root@yl-web yl]# 
```
## 三、配置mysql
### 1、编码
mysql配置文件为/etc/my.cnf

最后加上编码配置
```base
[mysql]
default-character-set =utf8
```
这里的字符编码必须和/usr/share/mysql/charsets/Index.xml中一致。

```base
<charset name="utf8">
  <family>Unicode</family>
  <description>UTF-8 Unicode</description>
  <alias>utf-8</alias>
  <collation name="utf8_general_ci"     id="33">
   <flag>primary</flag>
   <flag>compiled</flag>
  </collation>
  <collation name="utf8_bin"            id="83">
    <flag>binary</flag>
    <flag>compiled</flag>
  </collation>
</charset>
```
### 2、远程连接设置
把在所有数据库的所有表的所有权限赋值给位于所有IP地址的root用户。
```base
mysql> grant all privileges on *.* to root@'%'identified by 'password';
```
如果是新用户而不是root，则要先新建用户
```base
mysql>create user 'username'@'%' identified by 'password';  
```
此时就可以进行远程连接了。