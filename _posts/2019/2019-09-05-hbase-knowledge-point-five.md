---
layout: post
title:  Hbase知识点（五）HBase过滤器（Filter）
category: bigdata
tags: HBase Hadoop Java 大数据
keywords: bigdata,Java,Hbase，Hbase知识点，Hbase基本架构和原理,HBase过滤器
---
HBase 的基本 API，提供了包括增、删、改、查等的操作。相对而言，与传统的关系型数据库相比，查询略显苍白。只能根据特性的行键进行查询（Get）或者根据行键的范围来查询（Scan）。
为此 HBase 提供了高级的查询方法：过滤器 Filter 。

### 过滤器（Filter）

过滤器可以根据列族、列、版本等更多的条件来对数据进行过滤，基于 HBase 本身提供的三维有序（行键，列，版本有序），这些过滤器可以高效地完成查询过滤的任务，带有过滤器条件的 RPC 查询请求会把过滤器分发到各个 RegionServer（这是一个服务端过滤器），这样也可以降低网络传输的压力。

###  过滤器的两类参数

完成一个过滤的操作，至少需要两个参数。分别为：抽象的操作符、比较器（Comparator）。
有了这两个参数，我们就可以清晰的定义筛选的条件，过滤数据。

#### 抽象操作符（比较运算符）

HBase 提供了枚举类型的变量来表示这些抽象的操作符。

```
LESS < 小于
LESS_OR_EQUAL <= 小于等于
EQUAL = 等于
NOT_EQUAL <> 非等
GREATER_OR_EQUAL >= 大于等于
GREATER > 大于
NO_OP 排除所有
``` 

#### 比较器（Comparator）

比较器（Comparator），代表具体的比较逻辑，可以为字节级的比较、字符串级的比较等。

- BinaryComparator 按字节索引顺序比较指定字节数组，采用 Bytes.compareTo(byte[])
- BinaryPrefixComparator 跟前面相同，只是比较左端的数据是否相同
- NullComparator 判断给定的是否为空
- BitComparator 按位比较
- RegexStringComparator 提供一个正则的比较器，仅支持 EQUAL 和非 EQUAL
- SubstringComparator 判断提供的子串是否出现在 value 中

### HBase过滤器的分类

1、行键过滤器 RowFilter，筛选出匹配的所有的行。

```
Filter rowFilter = new RowFilter(CompareOp.GREATER, new BinaryComparator(Bytes.toBytes("row1")));
scan.setFilter(rowFilter);
```

2、列簇过滤器 FamilyFilter，筛选出匹配的列簇。

```
Filter familyFilter = new FamilyFilter(CompareOp.EQUAL, new BinaryComparator(Bytes.toBytes("cf1")));
scan.setFilter(familyFilter);
```

3、列过滤器 QualifierFilte，筛选出匹配的列。

```
Filter qualifierFilter = new QualifierFilter(CompareOp.EQUAL, new BinaryComparator(Bytes.toBytes("qualifier1")));
scan.setFilter(qualifierFilter);
```

4、值过滤器 ValueFilter，筛选出匹配的值。

```
Filter valueFilter = new ValueFilter(CompareOp.EQUAL, new SubstringComparator(value);
scan.setFilter(valueFilter);
```

5、时间戳过滤器 TimestampsFilter，

```
List<Long> list = new ArrayList<>();
list.add(1522469029503l);
TimestampsFilter timestampsFilter = new TimestampsFilter(list);
scan.setFilter(timestampsFilter);

```

6、FilterList：用于综合使用多个过滤器。其有两种关系：FilterList.Operator.MUST_PASS_ONE和FilterList.Operator.MUST_PASS_ALL，默认的是FilterList.Operator.MUST_PASS_ALL，顾名思义，它们分别是AND和OR的关系，并且FilterList可以嵌套使用FilterList，使我们能够表达更多的需求：

```
FilterList filterList = new FilterList(FilterList.Operator.MUST_PASS_ALL); 
filterList.addFilter(new ColumnPrefixFilter(keyPrefix.getBytes()));
filterList.addFilter(new QualifierFilter(CompareFilter.CompareOp.GREATER_OR_EQUAL,new BinaryComparator(Bytes.toBytes(cq1))));
scan.setFilter(filterList);
```

### 专用过滤器
1、单列值过滤器 SingleColumnValueFilter ----会返回满足条件的整行

```
SingleColumnValueFilter singleColumnValueFilter = new SingleColumnValueFilter(
                "info".getBytes(), //列簇
                "name".getBytes(), //列
                CompareOp.EQUAL, 
                new SubstringComparator("刘晨"));
//如果不设置为 true，则那些不包含指定 column 的行也会返回
singleColumnValueFilter.setFilterIfMissing(true);
scan.setFilter(singleColumnValueFilter);
```

2、单列值排除器 SingleColumnValueExcludeFilter 

```
SingleColumnValueExcludeFilter singleColumnValueExcludeFilter = new SingleColumnValueExcludeFilter(
                "info".getBytes(), 
                "name".getBytes(), 
                CompareOp.EQUAL, 
                new SubstringComparator("刘晨"));
singleColumnValueExcludeFilter.setFilterIfMissing(true);
        
scan.setFilter(singleColumnValueExcludeFilter);
```

3、前缀过滤器 PrefixFilter----针对行键

```
PrefixFilter prefixFilter = new PrefixFilter("9501".getBytes());
        
scan.setFilter(prefixFilter);
```

4、列前缀过滤器 ColumnPrefixFilter，包含某个前缀的所有列名

```
ColumnPrefixFilter columnPrefixFilter = new ColumnPrefixFilter(Bytes.toBytes("qualifier1"));
scan.setFilter(columnPrefixFilter);
```

5、列前缀过滤器 MultipleColumnPrefixFilter，包含指定多个列名（Qualifier）前缀

```
byte[][] prefixes = new byte[][]{Bytes.toBytes("qualifier1"), Bytes.toBytes("qualifier2")};
MultipleColumnPrefixFilter multipleColumnPrefixFilter = new MultipleColumnPrefixFilter(prefixes);
scan.setFilter(multipleColumnPrefixFilter);
```


6、分页过滤器 PageFilter
[分页方案](http://www.guodongkeji.com/newsshow-25-2108-1.html)


### 自定义过滤器

继承 FilterBase，然后打成 jar 包放到 $HBASE_HOEM/lib 目录下去，重启 HBase 集群。





