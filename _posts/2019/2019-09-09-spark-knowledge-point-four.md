---
layout: post
title:  Spark系列文章（四）:Spark之RDD
category: spark 
tags: 大数据 Spark
keywords: bigdata,Spark,Spark学习之路,Spark系列文章,Spark初识,Spark RDD
excerpt: "RDD（Resilient Distributed Dataset）叫做弹性分布式数据集，是Spark中最基本的数据抽象，代表一个不可变、可分区、里面的元素可并行计算的集合。"
---

## Spark系列文章（四）:Spark之RDD

### 一、RDD的概述
1.1、RDD是什么？

RDD（Resilient Distributed Dataset）叫做弹性分布式数据集，是Spark中最基本的数据抽象，代表一个不可变、可分区、里面的元素可并行计算的集合。

1.2、RDD的主要属性？

RDD 是Spark 中最基本的数据抽象，是一个逻辑概念，它可能并不对应次磁盘或内存中的物理数据，而仅仅是记录了RDD的由来,父RDD是谁，以及怎样从父RDD计算而来。

spark 源码里面对 RDD 的描述：
```
Internally, each RDD is characterized by five main properties:
A list of partitions
A function for computing each split
A list of dependencies on other RDDs
Optionally, a Partitioner for key-value RDDs (e.g. to say that the RDD is hash-partitioned)
Optionally, a list of preferred locations to compute each split on (e.g. block locations for an HDFS file)
```

可以知道，每个 RDD 有以下五部分构成：

- 一组分片（Partition），即数据集的基本组成单位。对于RDD来说，每个分片都会被一个计算任务处理，并决定并行计算的粒度。用户可以在创建RDD时指定RDD的分片个数，如果没有指定，那么就会采用默认值。默认值就是程序所分配到的CPU Core的数目。

- 每个Partition的计算函数。Spark中RDD的计算是以分片为单位的，每个RDD都会实现compute函数以达到这个目的。compute函数会对迭代器进行复合，不需要保存每次计算的结果。

- RDD之间的依赖关系。RDD的每次转换都会生成一个新的RDD，所以RDD之间就会形成类似于流水线一样的前后依赖关系。在部分分区数据丢失时，Spark可以通过这个依赖关系重新计算丢失的分区数据，而不是对RDD的所有分区进行重新计算。

- （可选的）对于key-value类型的RDD,则包含一个Partitioner，即RDD的分片函数。当前Spark中实现了两种类型的分片函数，一个是基于哈希的HashPartitioner，另外一个是基于范围的RangePartitioner。只有对于于key-value的RDD，才会有Partitioner，非key-value的RDD的Parititioner的值是None。Partitioner函数不但决定了RDD本身的分片数量，也决定了parent RDD Shuffle输出时的分片数量。

- （可选的）计算每个Parition所倾向的节点位置。存储存取每个Partition的优先位置（preferred location）。对于一个HDFS文件来说，这个列表保存的就是每个Partition所在的块的位置。按照“移动数据不如移动计算”的理念，Spark在进行任务调度的时候，会尽可能地将计算任务分配到其所要处理数据块的存储位置。

RDD是一个应用层面的逻辑概念。一个RDD多个分片。RDD就是一个元数据记录集，记录了RDD内存所有的关系数据。


### 二、Spark编程接口

Spark 程序设计流程一般如下：

步骤一：实例化 sparkContent 对象。sparkContent 封装了程序运行的上下文环境，包括配置信息、数据库管理器、任务调度等。<br>
步骤二：构造 RDD。可通过 sparkContent 提供的函数构造 RDD，常见的 RDD 构造方式分为：将 Scala集合转换为 RDD 和将 Hadoop 文件转换为 RDD。<br>
步骤三：在 RDD 基础上，通过 Spark 提供的 transformation 算子完成数据处理步骤。<br>
步骤四：通过 action 算子将最终 RDD 作为结果直接返回或者保存到文件中。<br>

Spark 提供了两大类编程接口，分别为 RDD 操作符以及共享变量。<br>
其中 RDD 操作符包括 transformation 和 action 以及 control API 三类；共享变量包括广播变量和累加器两种。<br>


### 三、创建 sparkContent 对象，封装了 Spark 执行环境信息

1、 创建conf ,封装了spark配置信息

```
SparkConf conf = new SparkConf().setAppName(appName); 
conf.set(“spark.master”, “local”); 
conf.set(“spark.yarn.queue”, “infrastructure”);
```

2、 创建 SparkContext，封装了调度器等信息

```
JavaSparkContext jsc = new JavaSparkContext(conf);
```

### 四、构建RDD

1、Java 集合构建
```
List<Integer> data = Arrays.asList(1, 2, 3, 4, 5); 
JavaRDD<Integer> rdd = jsc.parallelize(data, 3)
```

2、 将文本文件转换为 RDD

```
jsc.textFile(“/data”, 1) 
jsc.textFile(“/data/file.txt”, 1) 
jsc.textFile(“/data/*.txt”, 1) 
jsc.textFile(“hdfs://bigdata:9000/data/”, 1) 
jsc.sequenceFile(“/data”, 1) 
jsc.wholeTextFiles(“/data”, 1)
```


### 五、RDD transformation
transformation API 是惰性的，调用这些API比不会触发实际的分布式数据计算，而仅仅是将相关信息记录下来，直到action API才会开始数据计算。

Spark 提供了大量的 transformation API，下面列举了一些常用的API:


| API | 功能 |
| --- | --- |
| map(func) | 将 RDD 中的元素，通过 func 函数逐一映射成另外一个值，形成一个新的 RDD |
| filter(func) | 将 RDD 中使用 func 函数返回 true 的元素过滤出来，形成一个新的 RDD |
| flatMap(func | 类似于map，但每一个输入元素可以被映射为0或多个输出元素（所以func应该返回一个序列，而不是单一元素） |
| mapPartitions(func) | 类似于 map，但独立地在 RDD 的每一个分片上运行，因此在类型为T的 RDD 上运行时，func的函数类型必须是Iterator[T] => Iterator[U]|
| sample(withReplacement, fraction, seed) | 数据采样函数。根据fraction指定的比例对数据进行采样，可以选择是否使用随机数进行替换，seed 用于指定随机数生成器种子 |
| union(otherDataset) | 求两个 RDD (目标 RDD 与指定 RDD)的并集，并以 RDD 形式返回 |
| intersection(otherDataset) | 求两个 RDD (目标 RDD 与指定 RDD )的交集，并以 RDD 形式返回 |
| distinct([numTasks])) | 对目标 RDD 进行去重后返回一个新的 RDD |
| groupByKey([numTasks]) | 针对 key/value 类型的 RDD，将 key 相同的 value 聚集在一起。默认任务并发度与父 RDD 相同，可显示设置 [numTasks]大小 |
| reduceByKey(func, [numTasks]) | 针对 key/value 类型的 RDD，将 key 相同的 value 聚集在一起，将对每组value，按照函数 func 规约，产生新的 RDD  |
| aggregateByKey(zeroValue)(seqOp, combOp, [numTasks]) | 与 reduceByKey 类似，但目标 key/value 的类型与最终产生的 RDD 可能不同 |
| sortByKey([ascending], [numTasks]) | 针对 key/value 类型的 RDD，按照 key 进行排序，若 ascending 为 true，则为升序，反之为降序|
| join(otherDataset, [numTasks]) |  针对 key/value 类型的 RDD，对 (K,V) 类型的 RDD 和(K,W)类型的RDD上调用，按照 key 进行等值连接，返回一个相同key对应的所有元素在一起的(K,(V,W))的RDD  相当于内连接（求交集） |
| cogroup(otherDataset, [numTasks]) | 分组函数，对(K,V)类型的RDD和(K,W)类型的RD按照key进行分组，产生新的 (K,(Iterable<V>,Iterable<W>)) 类型的RDD|
| cartesian(otherDataset) | 求两个 RDD 的笛卡尔积 |
| coalesce(numPartitions)    | 重新分区, 缩减分区数，用于大数据集过滤后，提高小数据集的执行效率 |
| repartition(numPartitions) | 重新分区，将目标 RDD 的 partition 数量重新调整为 numPartitions， 少变多 |
| glom() | 将RDD中每个partition中元素转换为数组,并生 成新的rdd2 |
| mapValues() | 针对于(K,V)形式的类型只对V进行操作 |
| cache | RDD缓存，可以避免重复计算从而减少时间，cache 内部调用了 persist 算子，cache 默认就一个缓存级别 MEMORY-ONLY |
| persist | persist 可以选择缓存级别 |



### 六、RDD action
transformation 算子具有惰性执行的特性，他仅仅是记录一些原信息，知道遇到action算子才会触发相关transformation 算子的执行，

Spark 提供了大量的 action API，下面列举了一些常用的API:

| API | 功能 |
| --- | --- |
| reduce(func) | 将RDD中元素前两个传给输入函数，产生一个新的return值，新产生的return值与RDD中下一个元素（第三个元素）组成两个元素，再被传给输入函数，直到最后只有一个值为止 |
| collect() | 将 RDD 以数组的形式返回给 Driver,通过将计算后的较小结果集返回 |
| count() | 计算 RDD 中的元素个数 |
| first() | 返回 RDD 中第一个元素 |
| take(n) | 以数组的形式返回 RDD 前 n 个元素 |
| takeSample(withReplacement,num, [seed]) | 返回一个数组，该数组由从数据集中随机采样的num个元素组成，可以选择是否用随机数替换不足的部分，seed用于指定随机数生成器种子 |
| saveAsTextFile(path) | 将 RDD 存储到文本文件中，并一次调用每个元素的toString方法将之转换成字符串保存成一行 |
| saveAsSequenceFile(path)  | 针对 key/value 类型的 RDD,保存成 SequenceFile 格式文件|
| countByKey() | 针对 key/value 类型的 RDD,统计每个 key出现的次数，并以 hashmap 形式返回 |
| foreach(func) |  将 RDD 中的元素一次交给 func 处理 |
| aggregate | 先对分区进行操作，再总体操作  |
|aggregateByKey||
| lookup(key: K) | 针对 key/value 类型的 RDD, 指定key值，返回RDD中该K对应的所有V值。 |
| foreachPartition |  类似于 foreach，但独立地在 RDD 的每一个分片上运行，其中可嵌入foreach算子 |


### 七、RDD 的宽依赖和窄依赖

由于 RDD 是粗粒度的操作数据集，每个 Transformation 操作都会生成一个新的 RDD，所以 RDD 之间就会形成类似流水线的前后依赖关系；RDD 和它依赖的父 RDD（s）的关系有两种不同的类型，即窄依赖（Narrow Dependency）和宽依赖（Wide Dependency）。

宽依赖和窄依赖深度剖析图：
![](https://static.studytime.xin/image/articles/spring-boot1900685-74407830ac62d852.png?x-oss-process=image/resize,w_1100)

窄依赖：指的是子 RDD 只依赖于父 RDD 中一个固定数量的分区。
宽依赖：指的是子 RDD 的每一个分区都依赖于父 RDD 的所有分区。


RDD Stage：
![](https://static.studytime.xin/image/articles/spring-boot1900685-e784179c0fd1f80c.png?x-oss-process=image/resize,w_1100)

在 Spark 中，Spark 会将每一个 Job 分为多个不同的 Stage, 而 Stage 之间的依赖关系则形成了有向无环图，Spark 会根据 RDD 之间的依赖关系将 DAG 图（有向无环图）划分为不同的阶段，对于窄依赖，由于 Partition 依赖关系的确定性，Partition 的转换处理就可以在同一个线程里完成，窄依赖就被 Spark 划分到同一个 stage 中，而对于宽依赖，只能等父 RDD shuffle 处理完成后，下一个 stage 才能开始接下来的计算。

### 八、RDD 持久化

#### persist 和 cache 算子
RDD 持久化是 Spark 非常重要的特性之一。用户可显式将一个 RDD 持久化到内存或磁盘中，以便重用该RDD。RDD 持久化是一个分布式的过程，其内部的每个 Partition 各自缓存到所在的计算节点上。RDD 持久化存储能大大加快数据计算效率，尤其适合迭代式计算和交互式计算。

Spark 提供了 persist 和 cache 两个持久化函数，其中 cache 将 RDD 持久化到内存中，而 persist 则支持多种存储级别。

persist RDD 存储级别：

| 持久化级别	 | 含义 |
| --- | --- |
| MEMORY_ONLY | 以非序列化的Java对象的方式持久化在JVM内存中。如果内存无法完全存储RDD所有的partition，那么那些没有持久化的partition就会在下一次需要使用它的时候，重新被计算。 |
| MEMORY_AND_DISK | 同上，但是当某些partition无法存储在内存中时，会持久化到磁盘中。下次需要使用这些partition时，需要从磁盘上读取。
 |
| MEMORY_ONLY_SER | 同MEMORY_ONLY，但是会使用Java序列化方式，将Java对象序列化后进行持久化。可以减少内存开销，但是需要进行反序列化，因此会加大CPU开销 |
| MEMORY_AND_DSK_SER | 同MEMORY_AND_DSK。但是使用序列化方式持久化Java对象。 |
| DISK_ONLY | 使用非序列化Java对象的方式持久化，完全存储到磁盘上。 |
| MEMORY_ONLY_2
MEMORY_AND_DISK_2 | 如果是尾部加了2的持久化级别，表示会将持久化数据复用一份，保存到其他节点，从而在数据丢失时，不需要再次计算，只需要使用备份数据即可。 |

如何选择RDD持久化策略：

Spark 提供的多种持久化级别，主要是为了在 CPU 和内存消耗之间进行取舍。下面是一些通用的持久化级别的选择建议：

- 优先使用 MEMORY_ONLY，如果可以缓存所有数据的话，那么就使用这种策略。因为纯内存速度最快，而且没有序列化，不需要消耗CPU进行反序列化操作。
- 如果MEMORY_ONLY策略，无法存储的下所有数据的话，那么使用MEMORY_ONLY_SER，将数据进行序列化进行存储，纯内存操作还是非常快，只是要消耗CPU进行反序列化。
- 如果需要进行快速的失败恢复，那么就选择带后缀为_2的策略，进行数据的备份，这样在失败时，就不需要重新计算了。
- 能不使用DISK相关的策略，就不用使用，有的时候，从磁盘读取数据，还不如重新计算一次。

#### checkpoint
除了 cache 和 persist 之外，Spark 还提供了另外一种持久化：checkpoint, 它能将 RDD 写入文件系统，提供类似于数据库快照的功能。

于 cache 和 persist, 区别：
- Spark 自动管理（创建和回收）cache 和 persist 持久化的数据，而checkpoint持久化的数据需要有由户自己管理
- checkpoint 会清楚 RDD 的血统，避免血统过长导致序列化开销增大，而 cache 和 persist 不会清楚 RDD 的血统

代码实例：
```
sc.checkpoint("hdfs://spark/rdd"); // 设置存放目录
val data = sc.testFile("hdfs://bigdata:9000/input");
val rdd = data.map(..).reduceByKey(...);
rdd.checkpoint      // 设置checkpoint
rdd.count()         // action运算
```











