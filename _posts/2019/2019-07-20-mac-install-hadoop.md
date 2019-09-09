---
layout: post
title:  MAC下搭建Hadoop运行环境
category: bigdata 
tags: 大数据 Haoop mac 
keywords: bigdata,Haoop,mac Hadoop,Hadoop安装,hadoop
---

Hadoop环境的搭建和配置还是有些麻烦的，尤其是mac,此处整理下自己搭建的过程和步骤。

## 目录

### 安装 Java
Hadoop运行环境基于Java、所以Java环境是必须的。至于怎么安装JDK、自行谷歌，各位小哥，此处不做赘述。

### 配置ssh 
打开系统偏好设置-->共享--->远程登陆勾选上,且将当前hadoop运营用户加入允许远程登录用户列表中

![](https://static.studytime.xin/image/articles/20190720234625.png)

![](https://static.studytime.xin/image/articles/20190720235630.png)

避免使用hadoop命令时，多次输入密码
```
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

# 验证ssh 
ssh localhost
```

### 

### 下载Hadoop源码
本文以 [hadoop-2.7.3](https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/)为例，下载 hadoop-2.7.0.tar.gz 即可。下载完成后，解压到本地合适的目录下。
```
# 新建目录，存放安装的hadoop应用
mkdir Users/baihe/App

# 新建目录，存放应用的数据文件
mkdir Users/baihe/Data/

tar -zxvf hadoop-2.7.3.tar.gz -C /Users/baihe/App
```

### 添加 Hadoop 环境变量
```
vim ~/.bash_profile

# hadoop
export HADOOP_HOME=/Users/baihe/App/hadoop-2.7.3
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin

退出编辑模式，使用 :wq 保存修改，然后运行 source 命令使文件中的修改立即生效：

source ~/.bash_profile

```

查看Hadoop版本号:`hadoop version`

![](https://static.studytime.xin/image/articles/20190720231833.png)


### 修改Haoop配置文件

需要修改的 Hadoop 配置文件都在`/Users/baihe/App/hadoop-2.7.3/etc/hadoop/`下，包括：

hadoop-env.sh
core-site.xml
hdfs-site.xml
mapred-site.xml
yarn-site.xml

下面开始进行配置:
```
cd /Users/baihe/App/hadoop-2.7.3/etc/hadoop
```

### 修改 hadoop-env.sh 文件

设置 JAVA_HOME 的路径
```
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_191.jdk/Contents/Home
```

### 修改 core-site.xml 文件
默认情况下，Hadoop 将数据保存在/tmp 下，当重启系统时，/tmp 中的内容将被自动清空， 所以我们需要制定自己的一个 Hadoop 的目录，用来存放数据。另外需要配置 Hadoop 所使用的默认文件系统，以及 Namenode 进程所在的主机。
```
<property>
     <name>fs.defaultFS</name>
     <value>hdfs://localhost:9000</value>
</property>
<!--用来指定hadoop运行时产生文件的存放目录  自己创建-->
<property>
     name>hadoop.tmp.dir</name>
     <value>file://Users/baihe/Data/hadoop/tmp</value>
</property>
<property>
     <name>fs.trash.interval</name>
     <value>1440</value>
</property>
```

### 配置文件系统 hdfs-site.xml
该文件指定与 HDFS 相关的配置信息。需要修改 HDFS 默认的块的副本属性，因为 HDFS 默认 情况下每个数据块保存 3 个副本，而在伪分布式模式下运行时，由于只有一个数据节点，所 以需要将副本个数改为 1；否则 Hadoop 程序会报错。

```
vim hdfs-site.xml
```

修改配置文件

```
<property>
     <name>dfs.replication</name>
     <value>1</value>
</property>
<!--不是root用户也可以写文件到hdfs-->
<property>
     <name>dfs.permissions</name>
     <value>false</value>    <!--关闭防火墙-->
</property>
<!-- name node 存放 name table 的目录 -->
<property>
     <name>dfs.namenode.name.dir</name>
     <value>file:/Users/baihe/Data/hadoop/tmp/dfs/name</value>
</property>
<!-- data node 存放数据 block 的目录 -->
<property>
     <name>dfs.datanode.data.dir</name>
     <value>file:/Users/baihe/Data/hadoop/tmp/dfs/data</value>
</property>
```

### 配置计算框架 mapred-site.xml
在该配置文件中指定与 MapReduce 作业相关的配置属性，需要指定 JobTracker 运行的主机 文件夹中并没有 mapred-site.xml 文件，但提供了模板 mapred-site.xml.template 将其重命名为 mapred-site.xml 即可

```
mv mapred-site.xml.template mapred-site.xml
vim mapred-site.xml
```

修改配置文件
```
<property>
      <!--指定mapreduce运行在yarn上-->
      <name>mapreduce.framework.name</name>
      <value>yarn</value>
</property>
```

### 配置文件系统 yarn-site.xml

```
vim yarn-site.xml
```

修改配置文件
```
<!-- Site specific YARN configuration properties -->
<property>
<!-- mapreduce 执行 shuffle 时获取数据的方式 -->
      <name>yarn.nodemanager.aux-services</name>
      <value>mapreduce_shuffle</value>
</property>
<property>
      <name>yarn.resourcemanager.address</name>
      <value>localhost:18040</value>
</property>
<property>
      <name>yarn.resourcemanager.scheduler.address</name>
      <value>localhost:18030</value>
</property>
<property>
       <name>yarn.resourcemanager.resource-tracker.address</name>
       <value>localhost:18025</value>
</property>
<property>
       <name>yarn.resourcemanager.admin.address</name>
       <value>localhost:18141</value>
</property>
<property>
       <name>yarn.resourcemanager.webapp.address</name>
       <value>localhost:18088</value>
</property>
```

### Hadoop namenode 格式化
`hdfs namenode -format`
成功则会看到"successfully formatted"和"Exitting with status 0"的提示，若为 "Exitting with status 1" 则是出错。

### 启动 Hadoop 集群
```
/Users/baihe/App/hadoop-2.7.3/sbin
./start-all.sh
```

执行命令后，提示出入 yes/no 时，输入 yes
启动所有的 hadoop 服务，包括 hdfs 和 yarn

同时启动中可能会出现问题、继续向下看，下方会有描述。

### 查看进程是否启动
1、在 Hadoop 的终端执行 jps 命令，在打印结果中会看到 5 个进程，分别是 namenode、 secondarynamenode、datanode、resourcemanager、nodemanager， 如下图所示。 如果出现 了这 5 个进程表示主节点进程启动成功。

![](https://static.studytime.xin/image/articles/20190720233742.png)

2、Web UI 查看集群是否成功启动，浏览器中打开[http://localhost:50070/](http://localhost:50070/),以及[http://localhost:18088/](http://localhost:18088/)；检查 namenode 和 datanode 是否正常，检查 Yarn 是否正常。
![](https://static.studytime.xin/image/articles/20190720234052.png)
![](https://static.studytime.xin/image/articles/20190720234115.png)


























