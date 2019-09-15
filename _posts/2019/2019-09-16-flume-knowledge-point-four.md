---
layout: post
title:  Flume 知识点（四）Flume NG 常见数据流拓扑
category: bigdata 
tags: 大数据 Flume 
keywords: bigdata,Flume的常见数据流拓扑
excerpt: "如何构建数据流拓扑?"
---


## Flume 知识点（四）Flume NG 常见数据流拓扑

### 如何构建数据流拓扑？

为了使同Flume 收集日志，我们需要构建一个完整的数据流水线。可以按照以下步骤操作：

步骤一：确定流式数据获取方式。
步骤二：根据需求规划Agent,包括Agent数目，Agent依赖关系等。
步骤三：设置各个Agent,包括Source,Channel和Sink等组件的基本配置。
步骤四：测试构建数据流拓扑。
步骤五：生产环境部署该数据流拓扑。

### 数据流获取方式
Flume 支持多种方式供外部数据源将流式数据发送给Flume。

常用的方式包括：
1. 远程过程调用，这是最常用的一种方式，Flume 支持目前主流的 RPC 协议，包括 Avro 和 Thrift。
2. TCP 或 UDP，Flume 提供 Syslog Source，支持 TCP 和 UDP 两种协议，可通过这两种协议将数据传给 Flume。
3. EXEC 执行命令，Flume 提供了 Exec Source 


### 常见拓扑架构

- 单 Agent 采集数据
![](https://static.studytime.xin/image/articles/spring-boot20181231150136614.png)

- 多路串联
![](https://static.studytime.xin/image/articles/spring-boot20181231150136614.png)

- 多路合并
在流式日志收集系统中，常见的场景是大量日志产生，客户端将日志发送到少数几个聚集节点上，由这些节点对日志进行聚集合并后，写入后端的HDFS中。整个流程如图所属：

![](https://static.studytime.xin/image/articles/spring-boot20181231150442713.png)

在该数据流中，每个web Server 将流式日志发送到一个对应的 Flume Agent 上 ，Flume Agent 收到数据后，统一发送给一个汇总的 Agent，由它写入Hdfs上。

- 多路复用
Flume 支持将数据路由到多个目标系统中 ，这是通过 Flume 内置的多路复用功能实现的。典型拓扑如图：

![](https://static.studytime.xin/image/articles/spring-boot20181231150344785.png)

在该数据流中，Source 产生的数据按照类别被写入不同的Channel，之后由不同的 Sink 写入不同的目标系统中，需要注意的是 Sink 可写入另外一个Agent,进而实现Agent串联。


### Agent 配置方式

Flume 采用标准的 Java property 文件描述各个组件的配置，当 Agent 启动时，会读取本地对应的 Java property 文件，并按照配置要求创建和启动 Agent 内部各个组件。

```
bin/flume-ng agent -n LogAgent -c conf -f conf/logagent.properties -Dflume.root.logger=DEBUG,console
```

注各参数含义如下： 
- -n （或 --name ）: agent 地址，跟配置文件中的一致 
- -c （或 --conf ） : 配置文件所在目录，一般为 Flume 安装目录下的 conf 目录 
- -f （ --conf-file ） : agent 配置文件
- -D ：指定 JVM 参数，比如将 log 级别设置为 DEBUG 便为 -Dflume.root.logger=DEBUG,console

