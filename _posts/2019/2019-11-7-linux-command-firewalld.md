---
layout: post
title:  CentOS7 查看和关闭防火墙
category: linux 
tags: linux 
keywords: linux 防火墙 centos7关闭防火墙
excerpt: "CentOS 7.0默认使用的是firewall作为防火墙。"
---
## 目录

### 简述
CentOS 7.0默认使用的是firewall作为防火墙。

### firewall的基本启动/停止/重启命令

#### 查看防火墙状态

```
[root@application-server ~]# firewall-cmd --state
running
```

#### 停止防火墙/关闭防火墙
```
[root@application-server ~]# systemctl stop firewalld.service
[root@application-server ~]# firewall-cmd --state
not running
```

#### 启动防火墙
```
[root@application-server ~]# systemctl start firewalld.service
[root@application-server ~]# firewall-cmd --state
running
```

#### 设置开启启动防火墙
```
#设置开机启用防火墙
[root@application-server ~]# systemctl enable firewalld.service


#设置开机不启动防火墙
[root@application-server ~]# systemctl disable firewalld.service
```

#### 防火墙新增一个端口开发
```
firewall-cmd --zone=public --add-port=80/tcp --permanent

命令含义：
--zone #作用域
--add-port=80/tcp #添加端口，格式为：端口/通讯协议
--permanent #永久生效，没有此参数重启后失效

# 重启防火墙
firewall-cmd --reload
```

#### 其他常用命令

```
##查看防火墙状态，是否是running
firewall-cmd --state

##重新载入配置，比如添加规则之后，需要执行此命令
firewall-cmd --reload    

##列出支持的zone
firewall-cmd --get-zones   

##列出支持的服务，在列表中的服务是放行的
firewall-cmd --get-services    

##查看ftp服务是否支持，返回yes或者no
firewall-cmd --query-service ftp   

##临时开放ftp服务
firewall-cmd --add-service=ftp   

##永久开放ftp服务
firewall-cmd --add-service=ftp --permanent

##永久移除ftp服务
firewall-cmd --remove-service=ftp --permanent 

##永久添加80端口 
firewall-cmd --add-port=80/tcp --permanent    

##查看规则，这个命令是和iptables的相同的
iptables -L -n  

##查看帮助
man firewall-cmd                              
```