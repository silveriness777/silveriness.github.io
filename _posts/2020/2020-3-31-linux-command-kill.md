---
layout: post
title: 一天一组Linux命令：kill命令 和 如何批量杀死指定进程
category: linux 
tags: linux 
keywords: linux kill 
excerpt: "Linux中的kill命令用来终止指定的进程（terminate a process）的运行，是Linux下进程管理的常用命令。"
---

## 目录

### 简述
Linux中的kill命令用来终止指定的进程（terminate a process）的运行，是Linux下进程管理的常用命令。
通常，终止一个前台进程可以使用Ctrl+C键，但是，对于一个后台进程就须用kill命令来终止，我们就需要先使用ps/pidof/pstree/top等工具获取进程PID，然后使用kill命令来杀掉该进程。kill命令是通过向进程发送指定的信号来结束相应进程的。在默认情况下，采用编号为15的TERM信号。TERM信号将终止所有不能捕获该信号的进程。对于那些可以捕获该信号的进程就要用编号为9的kill信号，强行“杀掉”该进程。 

### 命令格式：
``kill [-9] 进程号``

### 命令功能：

发送指定的信号到相应进程。不指定型号将发送SIGTERM（15）终止指定进程。如果任无法终止该程序可用“-KILL” 参数，其发送的信号为SIGKILL(9) ，将强制结束进程，使用ps命令或者jobs 命令可以查看进程号。root用户将影响用户的进程，非root用户只能影响自己的进程。

### 命令参数：

- -l  信号，若果不加信号的编号参数，则使用“-l”参数会列出全部的信号名称

- -a  当处理当前进程时，不限制命令名和进程号的对应关系

- -p  指定kill 命令只打印相关进程的进程号，而不发送任何信号

- -s  指定发送信号

- -u  指定用户 

### 特殊注意

1、kill命令可以带信号号码选项，也可以不带。如果没有信号号码，kill命令就会发出终止信号(15)，这个信号可以被进程捕获，使得进程在退出之前可以清理并释放资源。也可以用kill向进程发送特定的信号。例如：

`kill -2 123`

它的效果等同于在前台运行PID为123的进程时按下Ctrl+C键。但是，普通用户只能使用不带signal参数的kill命令或最多使用-9信号。

2、kill可以带有进程ID号作为参数。当用kill向这些进程发送信号时，必须是这些进程的主人。如果试图撤销一个没有权限撤销的进程或撤销一个不存在的进程，就会得到一个错误信息。

3、可以向多个进程发信号或终止它们。

4、当kill成功地发送了信号后，shell会在屏幕上显示出进程的终止信息。有时这个信息不会马上显示，只有当按下Enter键使shell的命令提示符再次出现时，才会显示出来。

5、应注意，信号使进程强行终止，这常会带来一些副作用，如数据丢失或者终端无法恢复到正常状态。发送信号时必须小心，只有在万不得已时，才用kill信号(9)，因为进程不能首先捕获它。要撤销所有的后台作业，可以输入kill 0。因为有些在后台运行的命令会启动多个进程，跟踪并找到所有要杀掉的进程的PID是件很麻烦的事。这时，使用kill 0来终止所有由当前shell启动的进程，是个有效的方法。

### 使用实例：

#### 实例1：列出所有信号名称

```
[root@application_server shell]# kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX

```
说明：

只有第9种信号(SIGKILL)才可以无条件终止进程，其他信号进程都有权利忽略。 下面是常用的信号：


| 命令 | 数字 | 备注 |
| --- | --- | --- |
| HUP | 1 | 终端断线 |
| INT  | 2 | 中断（同 Ctrl + C） |
| QUIT | 3 | 退出（同 Ctrl + \） |
| TERM | 15 | 终止 |
| KILL | 9 | 强制终止 |
| CONT | 18 | 继续（与STOP相反， fg/bg命令） |
| STOP | 19 | 暂停（同 Ctrl + Z）） |



#### 实例2：得到指定信号的数值
```
kill -l KILL
kill -l SIGKILL
kill -l TERM
kill -l SIGTERM
```


#### 实例3：先用ps查找进程，然后用kill杀掉
```
[root@application_server shell]# ps -ef|grep vim
root     23810 23450  0 01:10 pts/1    00:00:00 grep --color=auto vim
[root@application_server shell]# kill 23810
bash: kill: (23810) - 没有那个进程
```


#### 实例4：彻底杀死进程
```
[root@application_server shell]# ps -ef|grep vim
root     23810 23450  0 01:10 pts/1    00:00:00 grep --color=auto vim
[root@application_server shell]# kill 23810
bash: kill: (23810) - 没有那个进程
[root@application_server shell]# kill -9 23810
bash: kill: (23810) - 没有那个进程
```


#### 实例5：杀死指定用户所有进程
```
方法一，过滤出hnlinux用户进程并杀死
kill -9 $(ps -ef | grep peidalinux)
kill -u peidalinux
```

#### 实例6：查询进程并批量杀死该进程
```
批量查询test进程，并杀死
ps -ef | grep test | grep -v grep | awk '{print $2}' | xargs -r kill -9
```

命令解析说明：

- `|` 管道符，用来隔开两个命令，管道符左边命令的输出会作为管道符右边命令的输入

- `ps` 命令用来列出系统中当前运行的进程，`ps -ef` 显示所有进程信息，连同命令行

- `grep` 命令用来过滤/搜索特定字符，`grep test`在这里为搜索过滤所有含有test名称的进程

- `grep -v grep` 显示不包含匹配文本的所有行，在这里为筛选出所有不包含grep名称的进程，对上一步的进程再做一次筛选（ps -ef列出了所有的进程，包括命令行）

- `awk` 在文件或字符串中基于指定规则浏览和抽取信息，把文件逐行读入，以空格为默认分隔符将每行切片，然后进行后续处理。这里利用`awk '{print $2}'` 将上一步中过滤得到的进程进行打印，$2表示打印第二个域（PID, 进程号） $0 表示所有域，$1表示第一个域，$n表示第n个域

- `xargs` 命令是给命令传递参数的过滤器，善于把标准数据转换成命令行参数。在这里则是获取前一个命令的输出，把它转换成命令行参数传递给后面的kill命令。-r选项表示如果输入为空，则不执行后面的命令

- `kill -9 `强制关闭进程
