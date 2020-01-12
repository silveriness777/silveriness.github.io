---
layout: post
title:  Spark系列文章（六）:Spark的共享变量
category: spark 
tags: 大数据 Spark
keywords: bigdata,Spark,Spark学习之路,Spark系列文章,Spark初识,Spark共享变量
excerpt: "Spark 提供了两种受限的共享变量：广播变量和共享变量，从而实现更加高效的运算。"
---

## 共享变量

### 广播变量 broadcast variable

#### 广播变量是什么？
广播变量是一种能够分发到集群各个节点上的只读变量，Driver 端将变量分发给各 Executor，Executor 只需保存该变量的一个副本，而不是每个 task 各分发一份，避免了 task 过多时候，Driver 的带宽成为系统瓶颈，以及 task 服务器上的资源消耗。Spark 实现了高效的广播算法保证广播变量得到高效的分发。

#### 图解广播变量

##### 未使用广播变量图解：
![](https://static.studytime.xin/image/articles/spring-boot1228818-20180421162057226-1988253385.png?x-oss-process=image/resize,w_600)

##### 使用广播变量图解：
![](https://static.studytime.xin/image/articles/spring-boot1228818-20180421162148572-1992224700.png?x-oss-process=image/resize,w_600)

#### Spark 如何创建广播变量和使用

1、 如何定义一个广播变量
```
val a = 3
val broadcast = sc.broadcast(a)
```
2、 还原一个广播变量
```
val c = broadcast.value
```
3、 代码使用实例
```
val arr = (0 until 1000).toArray

# 创建广播变量，对应的广播数据为数组
val barr = sc.broadccast(arr) 

# 广播变量的使用
val pbservedSizes = sc.parallelize(1 to 10 ,slices).map(_=>barr.value.size)
```
4、 使用广播变量注意事项
- 变量一旦被定义为一个广播变量，那么这个变量只能读，不能修改
- 能不能将一个 RDD 使用广播变量广播出去？答复是：不能，因为 RDD 是不存储数据的。可以将 RDD 的结果广播出去。
- 广播变量只能在 Driver 端定义，不能在 Executor 端定义。
- 在 Driver 端可以修改广播变量的值，在 Executor 端无法修改广播变量的值。
- 如果 Executor 端用到了 Driver 的变量，如果不使用广播变量在 Executor 有多少 task 就有多少 Driver 端的变量副本。
- 如果 Executor 端用到了 Driver 的变量，如果使用广播变量在每个 Executor 中只有一份 Driver 端的变量副本。


### 累加器 

#### 累加器是什么？
累加器类似于 MapReduce 中的分布式计数器，是一个整数值，能够在在各个任务中单独修改，之后自动汇总得到全局值。累加器常用于追踪状态的运行状态，方便对 Spark 的程序进行调试和监控。

#### 图解累加器

##### 未使用累加器

![](https://static.studytime.xin/image/articles/spring-boot1228818-20180421164701390-9845184.png?x-oss-process=image/resize,w_800)

#### 已使用累加器

![](https://static.studytime.xin/image/articles/spring-boot1228818-20180421165419534-240211041.png?x-oss-process=image/resize,w_800)

#### Spark 如何创建累加器和使用

1、 定义一个累加器
```
val a = sc.accumulator(0)
```

2、 还原一个累加器
```
val b = a.value
```

3、 代码使用实例
```
定义一个初始值为0，名为total的累加器
val totalPoints = sc.accumulator(0,"total")
定义一个初始值为0，名为hit的累加器
val hitPoints = sc.accumulator(0,"hit")
val count = sc.parallelize(1 until n,slices).map{
    val x = random * 2 -1
    val y = random * 2 -1
    totalPoints +=1 // 更新累加器
    if (x*x + y*y < 1) hitPoints += 1 // 更新累加器
}.reduce(_+_)

// 获取累加器值
val result = hitPoints.value / totalPoints.value;
```

##### 注意事项
- 累加器在 Driver 端定义赋初始值，累加器只能在 Driver 端读取最后的值，在 Excutor 端更新。
- 累加器不是一个调优的操作，因为如果不这样做，结果是错的