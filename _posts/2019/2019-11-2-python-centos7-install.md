---
layout: post
title:  CentOS7下将Python的版本升级为3.7
category: Python 
tags: Pythpn 
keywords: Python 
excerpt: "CentOS7下将Python的版本升级为3.7"
---

## 目录
### 查看系统python版本
```
[root@application_server_2 pkg]# python -V
Python 2.7.5
```

### 下载python3
python下载地址[https://www.python.org/ftp/python/](https://www.python.org/ftp/python/)
```
mkdir /data/pkg /data/app
cd /data/pkg
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
```

### 前置扩展
```
yum install make gcc gcc-c++ libffi-devel zlib*
```

### 解压编译安装
```
tar -zxvf Python-3.7.0.tgz -C /data/app
cd /data/app/python3.7.0
 ./configure --with-ssl --enable-optimizations
 make
 make install
```
![](https://static.studytime.xin/image/articles/20191106001612.png)
出现如图所示代表python编译安装成功。


### 更改默认的python版本

1. 检查当前默认的python2，默认自带的python位置在`/usr/bin/`目录下
```
[root@application_server_2 Python-3.7.0]# ll /usr/bin/python*
lrwxrwxrwx 1 root root    7 7月   2 15:03 /usr/bin/python -> python2
lrwxrwxrwx 1 root root    9 7月   2 15:03 /usr/bin/python2 -> python2.7
-rwxr-xr-x 1 root root 7216 6月  21 04:28 /usr/bin/python2.7
```
2. 检查新安装的python3，默认编译安装的python位置在`/usr/local/bin/`目录下
```
[root@application_server_2 Python-3.7.0]# ll /usr/local/bin/python*
lrwxrwxrwx 1 root root        9 11月  6 00:13 /usr/local/bin/python3 -> python3.7
-rwxr-xr-x 2 root root 12530336 11月  6 00:12 /usr/local/bin/python3.7
lrwxrwxrwx 1 root root       17 11月  6 00:13 /usr/local/bin/python3.7-config -> python3.7m-config
-rwxr-xr-x 2 root root 12530336 11月  6 00:12 /usr/local/bin/python3.7m
-rwxr-xr-x 1 root root     3097 11月  6 00:13 /usr/local/bin/python3.7m-config
lrwxrwxrwx 1 root root       16 11月  6 00:13 /usr/local/bin/python3-config -> python3.7-config
```
3. 修改默认的python，删除旧的python的软连接，创建新的python2和python3的软连接
```
cd /usr/bin/
rm -f python2
rm -f python
ln -s python2.7 python2
ln -s /usr/local/bin/python3 /usr/bin/python
```


### 更改默认的pip版本为pip3
```
ln -s /usr/local/bin/pip3 /usr/bin/pip
```

### 检查修改后的python版本
```
[root@application_server_2 bin]# pip -V
pip 10.0.1 from /usr/local/lib/python3.7/site-packages/pip (python 3.7)
[root@application_server_2 bin]# python -V
Python 3.7.0
[root@application_server_2 bin]# python2 -V
Python 2.7.5
[root@application_server_2 bin]# python3 -V
Python 3.7.0
[root@application_server_2 bin]# pip -V
pip 10.0.1 from /usr/local/lib/python3.7/site-packages/pip (python 3.7)
```
可以看到，默认打开的python版本已经是3.7.0，至此python的版本已经升级成功

### 后续的软件问题处理
python的默认版本修改为3.x之后，系统对于yum的调用会出问题，所以需要修改下yum的相关配置，由于上述的操作中，python已经指向python3，所以要改为明确指向python2。

```
/usr/bin/yum
/usr/libexec/urlgrabber-ext-down
```
将上述文件中的首行代码`#!/usr/bin/python`变更为`#!/usr/bin/python`。


### 修改pip源为阿里源
1. 新建对应的配置文件
```
cd /root/
mkdir .pip
cd .pip
touch pip.conf
echo 'set number' >> /etc/vimrc
```
2. 配置内容生成
```
vim /root/.pip/pip.conf

[global]
index-url=http://mirrors.aliyun.com/pypi/simple/
trusted-host=mirrors.aliyun.com
```
配置文件说明：

- `index-url=http://mirrors.aliyun.com/pypi/simple/`，指定阿里云源
- `trusted-host=mirrors.aliyun.com`，信任阿里云站点
