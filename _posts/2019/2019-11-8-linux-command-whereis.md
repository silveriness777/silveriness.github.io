---
layout: post
title:  一天一组Linux命令（文件查询）：whiereis
category: linux 
tags: linux 
keywords: linux whiereis
excerpt: "whereis 命令用于查找特定的文件，whereis 查找的速度非常快。"
---

### 简述
whereis 命令用于查找特定的文件，whereis 查找的速度非常快。

### whereis 命令的使用
`whereis [-bmsu] 文件或目录名`

选项与参数：
-b:定位可执行文件
-m:定位帮助文件
-s:定位源代码文件
-u:搜索默认路径下除可执行文件、源代码文件、帮助文件以外的其它文件

### 使用实例
1. whereis php 将于php相关的文件都查找出来
```
[baihe@application_server ~]$ whereis php
php: /usr/local/bin/php /usr/local/etc/php.ini /usr/local/php /usr/local/php-7.2.2/bin/php
```
2. whereis -b php 只将二进制文件 查找出来 
```
[baihe@application_server ~]$ whereis -b php
php: /usr/local/bin/php /usr/local/etc/php.ini /usr/local/php /usr/local/php-7.2.2/bin/php
```

3. whereis -m php  查出说明文档路径
4. whereis -s php  找source源文件。 

### 扩展说明

1. 相比于类型 find 查找文件命令为什么查询快？
和 find 相比，whereis 查找的速度非常快，这是因为 linux 系统会将 系统内的所有文件都记录在一个数据库文件中，当使用 whereis 和下面即将介绍的 locate 时，会从数据库中查找数据，而不是像 find命令那样，通过遍历硬盘来查找，效率自然会很高。
但是该数据库文件并不是实时更新，默认情况下时一星期更新一次，因此，我们在用whereis和locate 查找文件时，有时会找到已经被删除的数据，或者刚刚建立文件，却无法查找到，原因就是因为数据库文件没有被更新。 

