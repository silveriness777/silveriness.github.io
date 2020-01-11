---
layout: post
title:  Mac VMware Fusion CentOS7 安装、配置静态IP文档 
category: linux 
tags: linux 
keywords: mac Mac VMware Fusion centos7 静态ip
excerpt: "介绍的Mac VMware Fusion CentOS7配置静态IP的教程图解,希望对大家有所帮助"
---

## 安装文档

### 安装CentOS7

![](https://static.studytime.xin/image/articles/20200111133031.png)

这里我们要安装CentOS7 64位，所以选择CentOS7 64位配置

![](https://static.studytime.xin/image/articles/20200111133115.png)

![](https://static.studytime.xin/image/articles/20200111133127.png)

![](https://static.studytime.xin/image/articles/20200111133141.png)

![](https://static.studytime.xin/image/articles/20200111133152.png)

![](https://static.studytime.xin/image/articles/20200111133202.png)

### 我们点击存储后，vmware会自动帮我们创建一个虚拟机，但是我们还没有添加镜像，所以需要先将其关机

![](https://static.studytime.xin/image/articles/20200111133217.png)

![](https://static.studytime.xin/image/articles/20200111133228.png)

### 给新创建的虚拟机设置镜像
![](https://static.studytime.xin/image/articles/20200111133316.png)

![](https://static.studytime.xin/image/articles/20200111133326.png)


![](https://static.studytime.xin/image/articles/20200111133337.png)


![](https://static.studytime.xin/image/articles/20200111133346.png)

![](https://static.studytime.xin/image/articles/20200111133355.png)

![](https://static.studytime.xin/image/articles/20200111133404.png)

### 设置语言
这里说一下设置成英文的原因，因为英文是更国际化的语言，设置成英文的以后系统出问题了就是英文错误提示，通过英文错误提示更容易在网络上搜到解决办法，用中文的话出错了的话比较难找到解决办法
![](https://static.studytime.xin/image/articles/20200111133434.png)


### 设置时区，默认的是纽约的，这里改成中国上海
![](https://static.studytime.xin/image/articles/20200111133442.png)


### 其他设置
![](https://static.studytime.xin/image/articles/20200111133453.png)

![](https://static.studytime.xin/image/articles/20200111133510.png)

![](https://static.studytime.xin/image/articles/20200111133520.png)

![](https://static.studytime.xin/image/articles/20200111133530.png)

![](https://static.studytime.xin/image/articles/20200111133539.png)

### 设置root账号密码
![](https://static.studytime.xin/image/articles/20200111133553.png)

配置静态IP
1. 把网络配置改成nat模式

![](https://static.studytime.xin/image/articles/20200111133602.png)

![](https://static.studytime.xin/image/articles/20200111133613.png)

2. 通过Mac终端进入VMware Fusion的vmnet8目录
cd /Library/Preferences/VMware\ Fusion/vmnet8

![](https://static.studytime.xin/image/articles/20200111133738.png)

3. 查看nat.conf内容`cat nat.conf`
记住红框中的数据，下面配置时需要用到
![](https://static.studytime.xin/image/articles/20200111133850.png)

4. 查看cat dhcpd.conf内容`cat dhcpd.conf`
![](https://static.studytime.xin/image/articles/20200111135915.png)

注意range 这个是虚拟机允许选择的静态ip地址范围，自定义的静态ip地址必须要在这个范围内(本文打算使用172.16.104.130为例介绍)

5. 获取DNS(在mac系统偏好设置—>网络—>)

![](https://static.studytime.xin/image/articles/20200111134020.png)

![](https://static.studytime.xin/image/articles/20200111134136.png)

![](https://static.studytime.xin/image/articles/20200111135749.png)


6. 登录CentOS7、开始配置静态ip
![](https://static.studytime.xin/image/articles/20200111135249.png)

```php
cd /etc/sysconfig/network-scripts
找到ifcfg-en开头的文件,上图中我的是ifcfg-ens33
vim ifcfg-ens33
```
我们将它改成如下配置:
![](https://static.studytime.xin/image/articles/20200111134329.png)

7. 保存之后，重启服务使修改生效`service network restart`
![](https://static.studytime.xin/image/articles/20200111135338.png)

8. ping一下百度看看，成功Ping到
![](https://static.studytime.xin/image/articles/20200111135413.png)

9. 软件连接
![](https://static.studytime.xin/image/articles/20200111135437.png)

10. 注意事项
如果你换了一个地方上网的话，可能会发现你的虚拟机有不通了，那是因为DNS地址发生了变化，此时只需要再次编辑ifcfg-enxxx文件，然后加上你现在网络的DNS地址即可

