---
title: 搭建shadowsocks服务器
date: 2016-05-28 02:16:08
category: 翻墙
---

安装
Debian/Ubuntu:
```base
apt-get install python-pip
pip install shadowsocks
```
CentOS:
```based
yum install python-setuptools && easy_install pip
pip install shadowsocks
```
>easy_install貌似也可以装shadowsocks，但笔者装完之后未成功连接

```base
sudo easy_install shadowsocks
```

用vi新建一个配置文件：
```
vi /etc/shadowsocks.json
```
然后输入如下内容：
```json
{ 
   "server":"server_ip", 
   "server_port":12342,
   "local_address": "127.0.0.1", 
   "local_port":1080, 
   "password":"password",
   "timeout":300, 
   "method":"rc4-md5",
   "fast_open": false
}
```
保存退出。

后台启动和停止shadowsocks服务器：
```
ssserver -c /etc/shadowsocks.json -d start
ssserver -c /etc/shadowsocks.json -d stop
```