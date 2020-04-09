---
layout: post
title:  大数据Hadoop三节点分布式搭建
category: Hadoop
tags: Hadoop 
keywords: Hadoop 
excerpt: "大数据Hadoop三节点分布式搭建"
---

## 目录
### 虚拟机安装
### 安装以及配置定时时间同步
```
yum install -y ntpdate

#使用crontab -e命令添加定时时间同步配置 
[root@localhost ~]# crontab -e 
#执行crontab -e命令后，按键盘上的字母i进入编辑模式,输入一下内容，然后按一下键盘左上角的esc进行命令模式，按住shift输入:(冒号),在输入wq!回车完成保存.

#以下配置的意思是每分钟同步一次时间 */1 * * * * /usr/sbin/ntpdate us.pool.ntp.org; ~

:wq!

#等待一分钟左右使用date命令查看系统时间是否同步 
[root@localhost ~]# 
date 
Sun Jun 30 10:51:17 CST 2019 
[root@localhost ~]#
```

### 停止防火墙
```
#停止防火墙 
[root@node1 ~]# systemctl stop firewalld
#禁止防火墙随着系统启动而启动 
[root@node1 ~]# systemctl disable firewalld
#查看防火墙状态
[root@node1 ~]# systemctl status firewalld
```

![](https://static.studytime.xin/image/articles/20200408005959.png)

### 添加hadoop普通用户
在root用户基础上添加hadoop用户并设置sudo权限，后续软件的安装有些是在hadoop用户下完成。
```
useradd hadoop
passwd hadoop

#为hadoop用户添加sudo权限
vi /etc/sudoers
hadoop  ALL=(ALL)       ALL
```

### 新建安装包目录以及安装目录
建立统一的软件安装包目录和安装目录，其中software目录为压缩包存放目录，module为实际软件安装目录。

```
mkdir -p /opt/module
mkdir -p /opt/software
chown -R hadoop:hadoop /opt/
```
将两个文件权限更改为hadoop用户和组。
![](https://static.studytime.xin/image/articles/20200409124114.png)

### 安装JDK和Hadoop

#### 安装 JDK
```
# 进入软件安装包目录
cd /opt/software

# rz 命令打开本地需要上传jdk包， yum install lrzsz
rz 

# 安装jdk
rpm -ivh jdk-8u211-linux-x64.rpm
```

#### 配置java环境变量
```
#首先使用find命令找到java的安装目录
[root@node1 software]# find / -name java
/etc/pki/ca-trust/extracted/java
/etc/pki/java
/etc/alternatives/java
/var/lib/alternatives/java
/usr/bin/java
/usr/java
/usr/java/jdk1.8.0_211-amd64/bin/java
/usr/java/jdk1.8.0_211-amd64/jre/bin/java
[root@node1 software]#
```

```
vim ~/.bash_profile

JAVA_HOME=/usr/java/jdk1.8.0_211-amd64

#java环境变量加入到path中 
PATH=$PATH:$HOME/bin:$JAVA_HOME/bin

export JAVA_HOME export PATH
```

```
[root@node1 software]# source .bash_profile
[root@node1 software]# java -version
java version "1.8.0_211"
Java(TM) SE Runtime Environment (build 1.8.0_211-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.211-b12, mixed mode)
```

### 虚拟机克隆
以上就完成了一台机器的安装准备工作，配置集群的话需要再克隆2台，这里不再展示，克隆完成后需要再修改网卡和主机名。

### 修改机器名称以及设置免密登录

#### ip 配置
三节点ip规划如下：

| 节点名称 | ip |
| --- | --- |
| node1 | 172.16.225.131 |
| node2 | 172.16.225.132 |
| node3| 172.16.225.133 |
关于静态ip的设置，可以参考。

#### 主机名配置
分别登陆三台主机修改主机名称，命令如下：
```
hostnamectl set-hostname node1
hostnamectl set-hostname node2
hostnamectl set-hostname node3
```

### root用户的免密登录配置

#### 生成公钥和私钥
使用此命令：ssh-keygen -t rsa 分别在三台机器中都执行一遍,这里只在node1上做演示，其他两台机器也需要执行此命令。
```
[root@node1 ~]# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:PAdYETaFBOpuWU27t6LJQeo5H3vxhvJHljXFqnCm1tY root@node1
The key's randomart image is:
+---[RSA 2048]----+
|      .oB=.  .   |
|     . +..    o  |
|    . . o    o   |
|   .   +.oo +    |
|    . o S*.= .   |
|   . =  ++B E    |
|    = o..B.      |
|   o.o.=+.+.     |
|    oo*=.+.      |
+----[SHA256]-----+
[root@node1 ~]#
```

#### 配置hosts文件
```
#hosts文件中配置三台机器ip和主机名的映射关系,其他两台机器按照相同的方式操作. 
[root@node1 ~]# vi /etc/hosts

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

172.16.225.131 node1
172.16.225.132 node2
172.16.225.133 node3
```

#### 拷贝公钥文件
1. 将node1的公钥拷贝到node2,node3上
2. 将node2的公钥拷贝到node1,node3上
3. 将node3的公钥拷贝到node1,node2上
以下以node1为例执行秘钥复制命令：ssh-copy-id -i 主机名
```
[root@node1 ~]# ssh-copy-id -i node2
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@node2's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'node2'"
and check to make sure that only the key(s) you wanted were added.
```

#### 验证免密登录配置
```
[root@node1 ~]# ssh node2
Last failed login: Thu Apr  9 12:51:36 CST 2020 from 172.16.225.131 on ssh:notty
There were 2 failed login attempts since the last successful login.
Last login: Wed Apr  8 00:58:47 2020 from 172.16.225.1
[root@node2 ~]# exit
登出
Connection to node2 closed.
[root@node1 ~]# ssh node3
Last login: Thu Apr  9 12:57:21 2020 from 172.16.225.131
[root@node3 ~]# exit
登出
Connection to node3 closed.
```

#### 添加本地认证公钥到认证文件中
```
#进入到root用户的家目录下 
[root@node1 ~]# cd ~ 
[root@node1 ~]# cd .ssh/ 
#将生成的公钥添加到认证文件中 
[root@node1 .ssh]# cat id_rsa.pub >> authorized_keys 
[root@node1 .ssh]#
```


#### 安装hadoop

#### 创建hadoop 用户以及用户组

#### 把root用户的环境变量文件复制并覆盖hadoop用户下的.bash_profile
```
[root@node1 ~]# cp .bash_profile /home/hadoop/
```

### 配置haodoop用户的免密登录配置
```
[root@node1 ~]# su hadoop

配置haodoop用户的免密登录配置 和 上文 haodoop用户的免密登录配置 流程一样
```

特殊说明：重要的话必须说三次，三次，三次，再三次，看看下面的三行红色的字，不做，后面集群启动不了，让你后悔一万年，不懂照着做，啥都不要想,一个字就是干，一路操作猛如虎!

### 上传hadoop包以及解压

```
[hadoop@node1 root]$ cd /opt/software/
[hadoop@node1 root]$ rz

[hadoop@node1 root]$ tar -zxvf hadoop-2.6.0-cdh5.14.2_after_compile.tar.gz -C /opt/module/
```
![](https://static.studytime.xin/image/articles/20200409130557.png)

### 配置hadoop环境变量

#### 设置环境变量
```
vim ~/.base_profile

# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs
JAVA_HOME=/usr/java/jdk1.8.0_211-amd64
HADOOP_HOME=/opt/module/hadoop-2.6.0-cdh5.14.2

PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

export JAVA_HOME
export HADOOP_HOME
export PATH
```

#### 验证环境变量
```
[root@node1 ~]# source ~/.bash_profile
[root@node1 ~]# hadoop version
Hadoop 2.6.0-cdh5.14.2
Subversion Unknown -r Unknown
Compiled by root on 2019-08-07T08:39Z
Compiled with protoc 2.5.0
From source with checksum 302899e86485742c090f626a828b28
This command was run using /opt/module/hadoop-2.6.0-cdh5.14.2/share/hadoop/common/hadoop-common-2.6.0-cdh5.14.2.jar
[root@node1 ~]#
```

#### 配置hadoop-env.sh
这个文件只需要配置JAVA_HOME的值即可,在文件中找到export JAVA_HOME字眼的位置，删除最前面的#

```
vim /opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/hadoop-env.sh

export JAVA_HOME=/usr/java/jdk1.8.0_211-amd64
```

#### 4.配置core-site.xml


```
vim /opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/core-site.xml


<configuration>
        <!-- 指定hdfs的namenode主机的hostname -->
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://node1:9000</value>
        </property>
        <!-- io操作流的配置 -->
        <property>
                <name>io.file.buffer.size</name>
                <value>131072</value>
        </property>
                <!--hadoop集群临时数据存储目录-->
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/tmpdata</value>
        </property>
</configuration>
```

#### 配置hdfs-site.xml

```
vim /opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/hdfs-site.xml


<configuration>
        <!--namenode元数据存储目录-->
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>/opt/module/hadoop-2.6.0-cdh5.14.2/hadoop/hdfs/name/</value>
        </property>
        <!--指定block块的的大小-->
        <property>
                <name>dfs.blocksize</name>
                <value>268435456</value>
        </property>
        <!-- -->
        <property>
                <name>dfs.namenode.handler.count</name>
                <value>100</value>
        </property>
        <!--工作节点的数据块存储目录 -->
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>/opt/module/hadoop-2.6.0-cdh5.14.2/hadoop/hdfs/data/</value>
        </property>
        <!--block的副本数-->
        <property>
                <name>dfs.replication</name>
                <value>3</value>
        </property>
</configuration>

```

#### 配置mapred-site.xml
```
cp /opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/mapred-site.xml.template /opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/mapred-site.xml

vim /opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/mapred-site.xml


<configuration>
        <!--指定运行mapreduce的环境是yarn -->
i       <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>yarn.app.mapreduce.am.env</name>
                <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
        </property>
        <property>
                <name>mapreduce.map.env</name>
                <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
        </property>
        <property>
                <name>mapreduce.reduce.env</name>
                <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
        </property>
        <property>
                <name>mapreduce.application.classpath</name>
                <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*,$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
        </property>
</configuration>
```

#### 配置yarn-site.xml
```
vim /opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/yarn-site.xml


<configuration>
<!-- Site specific YARN configuration properties -->
<!--指定resourcemanager的位置-->
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>node1</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
        <property>
                <name>yarn.resourcemanager.address</name>
                <value>node1:18040</value>
        </property>
        <property>
                <name>yarn.resourcemanager.scheduler.address</name>
                <value>node1:18030</value>
        </property>
        <property>
                <name>yarn.resourcemanager.resource-tracker.address</name>
                <value>node1:18025</value>
        </property>
        <property>
                <name>yarn.resourcemanager.admin.address</name>
                <value>node1:18141</value>
        </property>
        <property>
                <name>yarn.resourcemanager.webapp.address</name>
                <value>node1:18088</value>
        </property>
</configuration>
```

#### 编辑slaves
此文件用于配置集群有多少个数据节点,我们把node2，node3作为数据节点,node1作为集群管理节点. 
配置/opt/module/hadoop-2.6.0-cdh5.14.2/etc/hadoop/目录下的slaves

```
[root@node1 hadoop]# vim slaves
#将localhost这一行删除掉
node2 node3
```

#### 远程复制hadoop到集群机器
```
#1.使用scp远程拷贝命令将root用户的环境变量配置文件复制到node2
scp ~/.bash_profile root@node2:~
#2.使用scp远程拷贝命令将root用户的环境变量配置文件复制到node3
scp ~/.bash_profile root@node2:~
#3.删除/opt/module/hadoop-2.6.0-cdh5.14.2/share/doc目录,这个目录存放的是用户手册，比较大，等会儿下面进行远程复制的时候时间比较长，删除后节约复制时间
rm -rf /opt/module/hadoop-2.6.0-cdh5.14.2/share/doc
#4.远程复制hadoop到集群机器node2
scp -r /opt root@node2:/
#5.远程复制hadoop到集群机器node3
scp -r /opt root@node3:/
```

#### 使集群所有机器环境变量生效
分别在node2,node3机器上使环境变量生效
```
source ~/.bash_prodile
```

#### 修改hadoop安装目录的权限

分别在node1,node2,node3上执行下面的命令

```
#1.修改目录所属用户和组为hadoop:hadoop
[root@node1 ~]# chown -R hadoop:hadoop /opt/
#2.修改目录所属用户和组的权限值为755
[root@node1 ~]# chmod -R 755 /opt/
[root@node1 ~]# chmod -R g+w /opt/
[root@node1 ~]# chmod -R o+w /opt/
```

#### 格式化hadoop
这一步很关键，如果没有什么问题说明前面配置的没有问题，只需要在node1节点上行进行格式化，注意！！！
在hadoop用户下，使用命令hdfs namenode -format

```
[root@node1 ~]# su - hadoop
上一次登录：四 4月  9 13:03:18 CST 2020pts/0 上
[hadoop@node1 ~]$ hdfs namenode -format
```
![](https://static.studytime.xin/image/articles/20200409134046.png)
如果看到了如上的提示，并且日志里没有报错说明格式化成功。

#### 启动集群
```
[hadoop@node1 ~]$ start-all.sh
This script is Deprecated. Instead use start-dfs.sh and start-yarn.sh
Starting namenodes on [node1]
node1: starting namenode, logging to /opt/module/hadoop-2.6.0-cdh5.14.2/logs/hadoop-hadoop-namenode-node1.out
node2: starting datanode, logging to /opt/module/hadoop-2.6.0-cdh5.14.2/logs/hadoop-hadoop-datanode-node2.out
node3: starting datanode, logging to /opt/module/hadoop-2.6.0-cdh5.14.2/logs/hadoop-hadoop-datanode-node3.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: starting secondarynamenode, logging to /opt/module/hadoop-2.6.0-cdh5.14.2/logs/hadoop-hadoop-secondarynamenode-node1.out
starting yarn daemons
starting resourcemanager, logging to /opt/module/hadoop-2.6.0-cdh5.14.2/logs/yarn-hadoop-resourcemanager-node1.out
node3: starting nodemanager, logging to /opt/module/hadoop-2.6.0-cdh5.14.2/logs/yarn-hadoop-nodemanager-node3.out
node2: starting nodemanager, logging to /opt/module/hadoop-2.6.0-cdh5.14.2/logs/yarn-hadoop-nodemanager-node2.out
# 使用jps显示java进程
[hadoop@node1 ~]$ jps
13264 ResourceManager
13522 Jps
12838 NameNode
13112 SecondaryNameNode
[hadoop@node1 ~]$
```

其他启动命令
```
# 单个启动
start-dfs.sh
start-yarn.sh
mr-jobhistory-daemon.sh start historyserver

# 单个关闭
stop-dfs.sh
stop-yarn.sh
mr-jobhistory-daemon.sh stop historyserver
```

#### 查看集群web ui
在浏览器地址栏中输入:http://node1:50070查看namenode的web界面.
![](https://static.studytime.xin/image/articles/20200409134448.png)