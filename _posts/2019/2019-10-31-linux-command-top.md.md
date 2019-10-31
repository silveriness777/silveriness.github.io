---
layout: post
title:  一天一组Linux命令：top
category: linux 
tags: linux 
keywords: linux top
excerpt: "top命令是Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况，类似于Windows的任务管理器。"
---
### 简述

top 命令是 Linux 下常用的性能分析工具，能够动态实时显示系统中各个进程的资源占用状况。

![](https://static.studytime.xin/image/articles/spring-boot20191101004234.png)

### top命令的使用

top使用格式

`top [-] [d] [p] [q] [c] [C] [S] [s] [n]`

top参数说明

- d: 指定每两次屏幕信息刷新之间的时间间隔`top -d 3`

- p: 通过指定监控进程ID来仅仅监控某个进程的状态`top -p 574`

- q: 该选项将使top没有任何延迟的进行刷新。如果调用程序有超级用户权限，那么- - top将以尽可能高的优先级运行。

- S: 指定累计模式`top -S`

- s: 使top命令在安全模式中运行。这将去除交互命令所带来的潜在危险。

- i: 使top不显示任何闲置或者僵死进程。

- c: 显示整个命令行而不只是显示命令名`top -c`

- n: 设置信息更新次数`top -n 2`

### top视图信息

视图信息主要分为以下两部分： 操作系统资源概况信息和进程信息

### 操作系统资源概况信息

![](https://static.studytime.xin/image/articles/spring-boot20191101004510.png)

#### 任务队列信息，与 uptime 命令执行结果相同

`top - 00:45:39 up 485 days, 10:40, 1 user, load average: 0.14, 0.08, 0.05`

- 00:43:53： 系统当前时间

- up 485 days, 10:39： 主机已运行时间

- 1 user： 用户连接数（不是用户数，who命令）

- load average: 0.14, 0.08, 0.05：系统平均负载，统计最近1, 5, 15分钟的系统平均负载

#### 进程信息

`Tasks: 1238 total, 1 running, 1237 sleeping, 0 stopped, 0 zombie`

- Tasks: 1238 total：进程总数

- 1 running：正在运行的进程数

- 1237 sleeping：睡眠的进程数

- 0 stopped：停止的进程数

- 0 zombie：僵尸进程数

#### CPU信息（当有多个CPU时，这些内容可能会超过两行）

`%Cpu(s): 1.6 us, 0.2 sy, 0.0 ni, 98.2 id, 0.0 wa, 0.0 hi, 0.0 si, 0.0 st`

- 1.5 us：用户空间所占CPU百分比

- 0.9 sy：内核空间占用CPU百分比

- 0.0 ni：用户进程空间内改变过优先级的进程占用CPU百分比

- 97.5 id：空闲CPU百分比

- 0.2 wa：等待输入输出的CPU时间百分比

- 0.0 hi：硬件CPU中断占用百分比

- 0.0 si：软中断占用百分比

- 0.0 st：虚拟机占用百分比

#### 内存信息

`KiB Mem : 32757552 total, 3287840 free, 17773204 used, 11696508 buff/cache`

- 32757552 total：物理内存总量

- 3287840 free：空闲的内存总量（free+used=total）

- 17773204 used：已使用的内存总量

- 11696508 buffers：用作内核缓存的内存量

#### swap信息

`KiB Swap: 4063228 total, 4004648 free, 58580 used. 12720656 avail Mem`

- 4063228 total：交换分区总量

- 4004648 used：已使用的交换分区总量

- 58580 free：空闲交换区总量

- 12720656 cached Mem：缓冲的交换区总量，内存中的内容被换出到交换区，然后又被换入到内存，但是使用过的交换区没有被覆盖，交换区的这些内容已存在于内存中的交换区的大小，相应的内存再次被换出时可不必再对交换区写入

### 进程信息区

```

PID USER PR NI VIRT RES SHR S %CPU %MEM IME+ COMMAND

18476 root 20 0 363816 47272 16144 S 7.3 0.1 0:41.36 php

```

- PID:进程id

- USER:进程所有者的用户名

- PR:优先级

- RUSER:Real user name（看了好多，都是这样写，也不知道和user有什么区别，欢迎补充此处）

- UID:进程所有者的id

- NI:nice值。负值表示高优先级，正值表示低优先级

- VIRT:进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES

- RES:进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA

- SHR:共享内存大小，单位kb

- S:进程状态,D=不可中断的睡眠状态、R=运行、S=睡眠、T=跟踪/停止、Z=僵尸进程

- %CPU:上次更新到现在的CPU时间占用百分比

- %MEM:进程使用的物理内存百分比

- TIME+:进程使用的CPU时间总计，单位1/100秒

- COMMAND:命令名/命令行

#### 常用交互命令

- c:显示进程命令的全路径与参数

- f:可以指定top显示的内容，如ppid、swap等都可以选择显示

- k:输入k之后可以kill掉指定的进程

- A:分类显示各种系统资源高的进程。可用于快速识别系统上的性能要求极高的任务，推荐使用

- h:获取top的命令帮助

- H:显示线程，默认只显示进程

- W[大写]:将当前设置写入~/.toprc文件中。这是写top配置文件的推荐方法
