---
layout: post
title: mysql配置之max_connections配置参数解析
category: mysql 
tags: [mysql]
keywords: max_connections mysql 
excerpt: "本文主要对 mysql 配置文件中 max_connections 配置参数，进行解析，其中包括参数作用，参数配置讲解，参数如何设置等。"
---

## 目录
### 概述
本文主要对 mysql 配置文件中 max_connections 配置参数，进行解析，其中包括参数作用，参数配置讲解，参数如何设置等。

### 与max_connections有关的特性
MySQL的 max_connections 参数是用来设置最大连接（用户）数。每个连接MySQL的用户均算作一个连接，max_connections 的默认值为 151。

MySQL无论如何都会保留一个用于管理员（SUPER）登陆的连接，用于管理员连接数据库进行维护操作，即使当前连接数已经达到了 max_connections。因此MySQL的实际最大可连接数为max_connections+1；
这个参数实际起作用的最大值（实际最大可连接数）为16384，即该参数最大值不能超过16384，即使超过也以16384为准；
增加max_connections参数的值，不会占用太多系统资源。系统资源（CPU、内存）的占用主要取决于查询的密度、效率等；
该参数设置过小的最明显特征是出现”Too many connections”错误。

### 如何查看当前mysql的max_connections的值

```
mysql> show variables like "max_connections";
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+
1 row in set (0.20 sec)

mysql> show global status like 'max_used_connections';
+----------------------+-------+
| Variable_name        | Value |
+----------------------+-------+
| Max_used_connections | 15    |
+----------------------+-------+
1 row in set (0.01 sec)
```

max_used_connections / max_connections * 100% （理想值 ≈ 85%），如果 max_used_connections 跟 max_connections 相同那么就是max_connections 设置过低或者超过服务器负载上限了，低于10%则设置过大。

### 如何查看正在连接中的进程
```
mysql> show processlist;
+-----+------+-----------+------+---------+------+----------+------------------+
| Id  | User | Host      | db   | Command | Time | State    | Info             |
+-----+------+-----------+------+---------+------+----------+------------------+
| 441 | root | localhost | NULL | Query   |    0 | starting | show processlist |
+-----+------+-----------+------+---------+------+----------+------------------+
1 row in set (0.01 sec)
```

### 怎样调整max_connections的值

#### 1. 具备操作权限的账户，命令行设置
```
mysql> set global max_connections = 500;
Query OK, 0 rows affected (0.01 sec)

mysql> show variables like "max_connections";
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 500   |
+-----------------+-------+
1 row in set (0.00 sec)
```

修改完成后实时生效，无需重启MySQL。

#### 2. mysql的ini配置文件my.ini
虽然上述方法可以起到作用，但是当mysql重启时这个设置会失效，更好的办法是修改mysql的ini配置文件my.ini.
```
# 打开mysql配置文件，默认在此路径
vim /etc/my.cnf

# 找到max_connections一行，修改为（如果没有，则自己添加）
max_connections = 1000
```
设置完成后重启mysql服务。


### 特殊说明
总体来说，max_connections 参数在服务器资源够用的情况下应该尽量设置大，以满足多个客户端同时连接的需求。否则将会出现类似”Too many connections”的错误。
具体设置时可以使用一个比较综合的数据， 个人设置一般是看服务器性能，以及观察当前连接数，从而选择一个合适的值。

用一个比较综合的数据， 个人设置一般是看服务器性能，以及观察当前连接数，从而选择一个合适的值。