---
layout: post
title:  一天一组Linux命令（文件查询）：which
category: linux 
tags: linux 
keywords: linux which
excerpt: "which 命令可以在 PATH 变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。"
---

## 

### 简述
which 命令可以在 PATH 变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。

### free 命令的使用
which 格式:
`which [-a] command`

free 主要参数参数说明：
- -n:指定文件名长度，指定的长度必须大于或等于所有文件中最长的文件名 
- -p:与-n参数相同，但此处的包括了文件的路径
- -w:指定输出时栏位的宽度
- -V:显示版本信息 

### 使用实例
1. which lsmod：查找文件、显示命令路径

```
➜  ~ which ifconfig
/sbin/ifconfig
```

which 是根据使用者所配置的 PATH 变量内的目录去搜寻可运行档的！所以，不同的 PATH 配置内容所找到的命令当然不一样的！

2. which which：用 which 去找出 which

```
[baihe@application_server ~]$ which which
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
	/usr/bin/alias
	/usr/bin/which
```
竟然会有两个 which ，其中一个是 alias 这就是所谓的『命令别名』，意思是输入 which 会等於后面接的那串命令！

3. which php

```
[baihe@application_server ~]$ which php
/usr/local/php/bin/php
```

### 扩展知识 file 命令

- 简述
file 命令用来查看文件内容类型，可以用来查看某个文件的基本数据，是属于ASCII或者是data文件，或者是binary等的信息，可以使用file命令来查看。

- file 使用实例
```
➜  ~ file ~/.bash_profile
/Users/baihe/.bash_profile: ASCII text
➜  ~ file /usr/bin/passwd
/usr/bin/passwd: Mach-O universal binary with 2 architectures: [x86_64:Mach-O 64-bit executable x86_64] [i386:Mach-O executable i386]
/usr/bin/passwd (for architecture x86_64):	Mach-O 64-bit executable x86_64
/usr/bin/passwd (for architecture i386):	Mach-O executable i386
```

