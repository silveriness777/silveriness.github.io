---
layout: post
title:  Hbase知识点（四）Hbase基本架构和原理
category: bigdata
tags: HBase Hadoop Java 大数据 架构
keywords: bigdata,Java,Hbase，Hbase知识点，Hbase基本架构和原理
---

HBase 采用了经典的 master/slave 架构，与 Hdfs 不同的是，他的 master 与 slave 不直接互联，而是引入 zookeeper 让两类服务解耦，这样使得 master 变得完全无状态，而避免了 master 宕机导致的整个集群不可用。

## 基本架构

### 错误图解

![](https://static.studytime.xin/image/articles/spring-boot1228818-20180402125111282-1966599087.png)

这张图是网上较多的博客中常见的错误概念，里面有一个错误点：应该是每一个 RegionServer 就只有一个 HLog，而不是一个 Region 有一个 HLog。


### 正确图解
![](https://static.studytime.xin/image/articles/spring-boot1228818-20180402125236874-140096659.png)

HBase 采用了经典的 master/slave 架构，与 Hdfs 不同的是，他的 master 与 slave 不直接互联，而是引入 zookeeper 让两类服务解耦，这样使得 master 变得完全无状态，而避免了 master 宕机导致的整个集群不可用。<br>
同时从 HBase 的架构图上可以看出，HBase 中的组件包括 Client、Zookeeper、HMaster、HRegionServer、HRegion、Store、MemStore、StoreFile、HFile、HLog等。下面将会简单介绍他们的功能。<br>

####  HMaster

HMaster 在整个集群中可以存在多个，但只可以有一个主 HMaster。主 HMaster 由 ZooKeeper 选举产生，当主 HMaster 故障后，系统可由 ZooKeeper 动态选举出新的主 HMaster 接管服务。<br>
HMaster 是无状态的（所有状态信息均保存在 ZooKeeper ）中，主 HMaster 挂掉后，因为 client 的读写服务是与 ZooKeeper 通信来完成的，所以不受影响。

HMaster 的主要职责如下:
- 协调 RegionServer：包括为 RegionServer 分配 Region，均衡各 RegionServer 的负载，发现失效的 RegionServer 并重新分配其上的 Region。
- 元信息管理：管理⽤户对 Table 的增、删、改、查操作，（处理 Schema 更新请求，表的创建，删除，修改，列簇的增加等等）。
- HDFS 上的垃圾文件（HBase）回收

#### ZooKeeper
ZooKeeper 内部存储着有关hbase的重要元信息和状态信息，担任着Master与RegionServer之间的服务协调角色。

ZooKeeper的主要职责如下:
- 保证任何时候，集群中只有一个HMaster，同时出现故障时，选举新的HMaster，解决单点故障问题。
- 存储所有 Region 的寻址入口，-ROOT-表在哪台服务器上。-ROOT-这张表的位置信息
- 实时监控 RegionServer 的状态，将 RegionServer 的上线和下线信息，实时通知master.
- 存储 HBase 的 schema 和 table 元数据。而HMaster则是管理着这些原信息的操作。

#### Client
Client 提供 HBase 访问接口，与 RegionServer 交互读写数据，并维护cache加快对 HBase 的访问速度。

HBase 有两张特殊表：
.META.：记录了用户所有表拆分出来的的 Region 映射信息，.META.可以有多个 Regoin<br>
-ROOT-：记录了.META.表的 Region 信息，-ROOT-只有一个 Region，无论如何不会分裂<br>

Client 访问用户数据前需要首先访问 ZooKeeper，找到-ROOT-表的 Region 所在的位置，然后访问-ROOT-表，接着访问.META.表，最后才能找到用户数据的位置去访问，中间需要多次网络操作。同时Client端会做cache缓存。

#### RegionServer
HRegionServer 内部管理了⼀系列 HRegion 对象。<br>
RegionServer 负责 Master 分配给他的 Regin 的存储和管理（比如**Region切分**），并与Client交互，处理读写请求。<br>

#### HRegion
每个 HRegion 对应了 Table 中的⼀个 Region，HRegion 中由多个 HStore 组成。<br>
Region 是 HBase 中分布式存储和负载均衡的最小单元，即不同的 Region 可以分别在不同的 Region Server 上，但同一个 Region 是不会拆分到多个server 上。<br>
Region 按⼤⼩分割的，每个表开始只有⼀个 Region，随着数据增多， Region 不断增⼤，当增⼤到⼀个阀值的时候，Region 就会等分会两个新的 Region，之后会有越来越多的 Region。每⼀个 Region 都包含所有的列簇。<br>

#### Store
Region 内部包含多个列簇，每⼀个列簇交给⼀个 Store 来存储。每个 Store 将数据分为两部分：<br>
存储在内存中的数据( MemStore )和存储在⽂件系统的数据( StoreFile )。HBase 以 store 的大小来判断是否需要切分 region。

#### MemStore
MemStore 是存储在内存中的，只存储新增的和修改过的数据，并在 MemStore 的大小达到一个阀值（默认128MB）时，将数据 flush 到 StoreFile 中。

#### StoreFile
MemStore 内存中的数据写到文件后就是 StoreFile，StoreFile 底层是以 HFile 的格式保存。

#### HFile
HFile 是 Hadoop 的二进制格式文件，实际上 StoreFile 就是对 Hfile 做了轻量级包装，即 StoreFile 底层就是 HFile。

#### HLog
HLog(WAL Log)：WAL意为 Write Ahead Log，保存在 HDFS上的日志文件，用来做灾难恢复使用，HLog 记录数据的所有变更，一旦 RegionServer 宕机，就可以从 Log 中进行恢复。

#### BlockCache
BlockCache：读缓存，负责缓存频繁读取的数据，采用了LRU置换策略

## HBase 内部原理
HBase 是构建在 HDFS 之上的，它利用 HDFS 可靠的存储数据文件，其内部包含 Region 定位，读写流程管理和文件管理等实现。

### Region 定位、寻址机制

Hbase 支持 Put、get、delete、scan等操作，所有这些操作的基础是Region 定位。<br>
HBase-0.96 版本以前，HBase 有两个特殊的表，分别是-ROOT-表和.META.表，其中-ROOT的位置存储在 ZooKeeper 中，-ROOT-本身存储了.META. Table 的 RegionInfo 信息，并且-ROOT不会分裂，只有一个 Region。<br>
而.META.表可以被切分成多个 Region。用户需要 3 次请求才能直到用户 Table 真正的位置，这在一定 程序带来了性能的下降。<br>
在 0.96 之前使用 3 层设计的主要原因是考虑到元数据可能需要很大。但是真正集群运行，元数据的大小其实很容易计算出来。<br>
在 BigTable 的论文中，每行 METADATA 数据存储大小为 1KB 左右，如果按照一个 Region 为 128M 的计算，3 层设计可以支持的 Region 个数为 2^34 个，采用 2 层设计可以支持 2^17（131072）。<br>
那么 2 层设计的情 况下一个集群可以存储 4P 的数据。这仅仅是一个 Region 只有 128M 的情况下。如果是 10G 呢? 因此，通过计算，其实 2 层设计就可以满足集群的需求。因此在 0.96 版本以后就去掉 了-ROOT-表了。<br>

regin定位访问基本步骤如下:

![](https://static.studytime.xin/image/articles/spring-boot20190901101541.png) 

- 步骤一：Client 请求 ZooKeeper 获取 hbase:meta 系统表所在的 RegionServer 地址
- 步骤二：Client 请求 hbase:meta 所在的 RegionServer 获取访问数据所在的 RegionServer 地址，Client 会将 hbase:meta 的相关信息 cache 下来，以便下一次快速访问。
- 步骤三：Client 请求数据所在的 RegionServer，获取所需要的数据。

需要注意的是，客户端首次执行读写操作时，才需要定位 hbase:meta表所在的位置，之后会将其缓存到本地。除非因 region移动导致缓存失败，客户端才会重新读取 hbase:meta 表位置，并更新缓存。


### Region Server 读写操作
Hbase 中最重要的操作是写操作和读操作。

#### 写流程：

为了提高Hbase写效率，避免随机写性能低下，RegionServer将所有收到暂时写入内存，之后再顺序刷新到磁盘上，进而将随机写转化成顺序写以提升性能。<br>
具体流程如下：
- 步骤一：RegionServer 收到写请求后，将写入数据以追加的方式写入Hdfs上的日志文件，该日止文件被称为Write Ahead log（wal）。wal主要作用是当RegionServer突然宕机后重新恢复丢失的数据。
- 步骤二：RegionServer 将数据写入内存数据memstore中，之后响应客户端数据写入成功。当memstore所占内存达到一定发之后，Regionserver会将数据刷新到Hdfs中，保存成Hfile格式的文件。

#### 读流程：
由于写流程可能使得数据位于内存中或者磁盘中，因此读取数据时，需要考虑多种情况。需要从多个数据存放位置中寻找数据，包括读缓存BlockCache，写缓存Memstore，以及磁盘上的Hfile文件，并将读到的数据合并在一起返回给用户。<br>

具体流程如下：<br>
- 步骤一：扫描器查找读缓存 BlockCache，它内部缓存了最近读取过的数据
- 步骤二：扫描器查找写缓存 MemCache，它内部缓存了最近写入的数据
- 步骤三：如果在 BlockCache 和 MemCache 中未找到目标数据，HBase 将读取中 Hfie，以获取想要的数据

#### 数据 Flush 流程：
- 步骤一：当 MemStore 数据达到阈值（默认是128M，老版本是64M），将数据刷到硬盘，将内存中的数据删除，同时删除 HLog 中的历史数据
- 步骤二：并将数据存储到 Hdfs 中
- 步骤三：在 HLog 中做标记点

#### 数据合并过程：
当数据块达到4块，HMaster 触发合并操作，Region 将数据块加载到本地，进行合并<br>
当合并的数据超过256M，进行拆分，将拆分后的 Region 分配给不同的 HRegionServer 管理<br>
当HRegionServer宕机后，将HRegionServer上的 HLog 拆分，然后分配给不同的 HRegionServer 加载，修改.META.<br>
注意：HLog会同步到 Hdfs<br>






