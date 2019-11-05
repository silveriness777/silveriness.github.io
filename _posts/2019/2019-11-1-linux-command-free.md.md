---
layout: post
title:  一天一组Linux命令：free
category: linux 
tags: linux 
keywords: linux free
excerpt: "free 命令可以显示 Linux 系统中空闲的、已用的物理内存、swap内存，以及被内核使用的buffer。在Linux系统监控工具中，free命令是被经常使用的命令之一。"
---

## free命令
### 简述
free 命令可以显示 Linux 系统中空闲的、已用的物理内存、swap内存，以及被内核使用的buffer。在Linux系统监控工具中，free命令是被经常使用的命令之一。
![](https://static.studytime.xin/image/articles/20191106002320.png)

### free 命令的使用
free 格式:
`free [－b　－k　－m] [－s delay] [－t] [－V]`

free 主要参数参数说明：
- -b:以Byte为单位显示内存使用情况。 
- -k:以KB为单位显示内存使用情况。 
- -m:以MB为单位显示内存使用情况。
- -g:以GB为单位显示内存使用情况。 
- -o:不显示缓冲区调节列。 
- -s<间隔秒数>: 持续观察内存使用状况。 
- -t:显示内存总和列。 
- -V:显示版本信息。 

### 响应结果参数说明

- total：内存总数，物理内存总数
- used：已经使用的内存数
- free：空闲的内存数
- shared：多个进程共享的内存总额
- buffers Buffer：缓存内存数
- cached Page：缓存内存数
- -buffers/cache：应用使用内存数
- +buffers/cache：应用可用内存数
- Swap	交换分区，虚拟内存

### 使用实例
#### free -b：以bytes为单位来显示内存的信息
```
[root@application_server_2 data]# free -b
              total        used        free      shared  buff/cache   available
Mem:     1927528448   107917312   294449152      450560  1525161984  1619210240
Swap:             0           0           0
```
#### free -k：以kb为单位来显示内存的信息
```
[root@application_server_2 data]# free -k
              total        used        free      shared  buff/cache   available
Mem:        1882352      105708      287100         440     1489544     1580892
Swap:             0           0           0
```
#### free -m：以m为单位来显示内存的信息
```
[root@application_server_2 data]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1838         103         280           0        1454        1544
Swap:             0           0           0
```

#### free -g：以G为单位来显示内存的信息
```
[root@application_server_2 data]# free -g
              total        used        free      shared  buff/cache   available
Mem:              1           0           0           0           1           1
Swap:             0           0           0
```

#### free -h：以适于人类可读方式显示内存信息。-h与其他命令最大不同是-h选项会在数字后面加上适于人类可读的单位
```
[root@application_server_2 data]# free -h
              total        used        free      shared  buff/cache   available
Mem:           1.8G        103M        280M        440K        1.4G        1.5G
Swap:            0B          0B          0B
```
#### free -l：显示高低内存的利用率
#### free -t：显示linux的全部内存
#### free -s N：表示每隔N秒打印一次内存信息
#### free -c N：表示重复打印内存信息N次
#### free -V：显示版本信息
#### free -hs 3：以人类可读的方式，每隔3s打印一次内存信息
#### free -s 2 -c 4：每隔2秒打印一次内存信息，共打印4次

### 重点说明
```
[root@application_server_2 data]# free -k
              total        used        free      shared  buff/cache   available
Mem:        1882352      105708      287100         440     1489544     1580892
Swap:             0           0           0
```

从上述命令行中可以看出：
对于操作系统而言，buff/cache 是属于被使用的，所以可以看到系统显示的可用内存是287100kb，已用内存是 105708kb。<br>
但就实际场景，应用程序角度而言，对于应用程序来说，buff/cache 又是可用的。<br>
这主要是因为在 Linux 系统的设计思想，内存不用白不用，系统会尽可能的 cache 和 buffer 一些数据，来提高文件读取的性能，当应用程序需在用到内存的时候，buffer/cached会很快地被回收。<br>
故而应用程序角度而言，真实的可用内存为 系统free memory + buffers+cached。<br>
这也是在实际场景中使用 free 命令查看机器空闲内存时，会发现free的值很小的原因，不慌看了上文就知道原因了。<br>


