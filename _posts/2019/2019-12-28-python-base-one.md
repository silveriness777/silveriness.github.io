---
layout: post
title:  Python基础(一)：初始python和基础知识
category: Python 
tags: Pythpn 
keywords: Python 
excerpt: "python 的创始人为吉多·范罗苏姆（Guido van Rossum）。1989年的圣诞节期间，吉多·范罗苏姆为了打发无聊的圣诞节而用C编写的一个编程语言。"
---

## 目录
### python 发展历程
python 的创始人为吉多·范罗苏姆（Guido van Rossum）。1989年的圣诞节期间，吉多·范罗苏姆为了打发无聊的圣诞节而用C编写的一个编程语言。

关键常识：
1. Python的意思是蟒蛇，源于作者喜欢的一部电视剧 
2. Python的作者是Guido van Rossum（龟叔）
3. Python正式诞生于1991年

### 标志描述
Life is shot, you need Python. 人生苦短，我用Python。

### Python解释器
Python的解释器如今有多个语言实现，我们常用的是CPython（官方版本的C语言实现）。当我们编写Python代码时，我们得到的是一个包含Python代码的以.py为扩展名的文本文件。要运行代码，就需要Python解释器去执行.py文件。

- CPython
- IPython
- PyPy
- Jython
- IronPython

### Python版本
Python目前有两个版本，Python2和Python3，Python2最新版为2.7.12，现阶段大部分公司用的是Python2。

### python的优缺点

优点：
- Python的定位是“优雅”、“明确”、“简单”。
- 开发效率非常高————Python有非常强大的第三方库，基本上你想通过计算机实现任何功能，Python官方库里都有相应的模块进行支持，直接下载调用后，在基础库的基础上再进行开发，大大降低开发周期，避免重复造轮子。
- 高级语言————当你用Python语言编写程序的时候，你无需考虑诸如如何管理你的程序使用的内存一类的底层细节
- 可移植性————由于它的开源本质，Python已经被移植在许多平台上（经过改动使它能够工 作在不同平台上）。如果你小心地避免使用依赖于系统的特性，那么你的所有Python程序无需修改就几乎可以在市场上所有的系统平台上运行
- 可扩展性————如果你需要你的一段关键代码运行得更快或者希望某些算法不公开，你可以把你的部分程序用C或C++编写，然后在你的Python程序中使用它们。
- 可嵌入性————你可以把Python嵌入你的C/C++程序，从而向你的程序用户提供脚本功能。

缺点：
- 速度慢。
- 代码不能加密。
- 线程不能利用多CPU问题。


### Python应用场景
- 云计算: 云计算最火的语言， 典型应用OpenStack。
- WEB开发: 众多优秀的WEB框架，众多大型网站均为Python开发，Youtube, Dropbox, 豆瓣。。。， 典型WEB框架有Django。
- 科学运算、人工智能: 典型库NumPy, SciPy, Matplotlib, Enthought librarys,pandas
- 系统运维: 运维人员必备语言。
- 金融：量化交易，金融分析，在金融工程领域。
- 图形GUI: PyQT, WxPython,TkInter。

### 第一个Python程序
```
print("hello world")
```

### 注释
Python的注释分为两种：
- 单行注视：# 被注释内容
- 多行注释：""" 被注释内容 """

### 变量
程序就是⽤来处理数据的，⽽变量就是⽤来存储数据的
```
# _*_coding:utf-8_*_

# 变量声明
message = 'hello world'
print(message)
```
变量定义的规则：
- 变量名只能是 字母、数字或下划线的任意组合
- 变量名的第一个字符不能是数字
- 关键字不能声明为变量名
- 见名知意


特殊说明：
1. python和php一样都会弱类型语言，不需要提前指定，而是根据上下文环境自动分配内存空间
2. python⼀些具有特殊功能的标示符，这就是所谓的关键字，python已经使⽤的了，所以不允许开发者⾃⼰定义和关 键字相同的名字的标示符
3. 为了更充分的利用内存空间以及更有效率的管理内存，变量是有不同的类型的

### 关键字
python一些具有特殊功能的标示符，这就是所谓的关键字
关键字，是python已经使用的了，所以不允许开发者自己定义和关键字相同的名字的标示符

```
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

### 数据类型
![](https://static.studytime.xin/image/articles/20191230011023.png)

### 输入
python3版本中使用input()接受输入参数
```
message = input("请输入数据")
print(message)
print(type(message))
```

### 输出
- 普通的输出
```
print("输出")
```
- 格式化输出
```
age = 18
name = "da bai"
print("我的姓名是%s,年龄是%d" % (name, age))
```
在程序中，看到了%这样的操作符，这就是Python中格式化输出。

创建格式输出符号：
```
格式符号            转换
%c                字符
%s                通过str() 字符串转换来格式化
%i                有符号⼗进制整数
%d                有符号⼗进制整数
%u                ⽆符号⼗进制整数
%e                索引符号（⼩写'e'）
%E                索引符号（⼩写'E'）
%f                浮点实数
```

- 换行输出
```
print("1234567890\n-------")
```

### 运算符
- 算术运算符
![](https://static.studytime.xin/image/articles/20191230011842.png)

- 比较运算符
![](https://static.studytime.xin/image/articles/20191230011854.png)

- 赋值运算符
![](https://static.studytime.xin/image/articles/20191230011910.png)

- 逻辑运算符
![](https://static.studytime.xin/image/articles/20191230011938.png)

- 成员运算
![](https://static.studytime.xin/image/articles/20191230011947.png)

- 身份运算
![](https://static.studytime.xin/image/articles/20191230011958.png)

- 位运算
![](https://static.studytime.xin/image/articles/20191230012008.png)

- 运算符优先级
![](https://static.studytime.xin/image/articles/20191230012038.png)


### 常用的数据类型转换
```
函数                  描述
int(x [,base ])      将x转换为一个整数
long(x [,base ])     将x转换为一个长整数
float(x )           将x转换到一个浮点数
str(x )             将对象 x 转换为字符串
repr(x )	        将对象 x 转换为表达式字符串
eval(str )	        用来计算在字符串中的有效Python表达式,并返回一个对象
tuple(s )	        将序列 s 转换为一个元组
list(s )	        将序列 s 转换为一个列表
chr(x )            将一个整数转换为一个字符
unichr(x )	       将一个整数转换为Unicode字符
ord(x )	           将一个字符转换为它的整数值
hex(x )	           将一个整数转换为一个十六进制字符串
oct(x )	           将一个整数转换为一个八进制字符串
```
实例：
```
a = '100' 
b = int(a)
print("a=%d"%b)
```

### 作业练习
1. 说出变量名字，可以由哪些字符组成
2. 写出变量命名时的规则
3. 说出什么是驼峰法（大驼峰、小驼峰）
4. 编写程序，完成以下要求：
提示用户进行输入数据
获取用户的数据数据（需要获取2个）
对获取的两个数字进行求和运行，并输出相应的结果
5. 编写程序，完成以下要求：
提示用户进行输入数据
获取用户的数据数据（需要获取2个）
对获取的两个数字进行减法运行，并输出相应的结果
6. 编写程序，完成以下信息的显示:
> ==================================
    =        欢迎进入到身份认证系统V1.0
    = 1. 登录
    = 2. 退出
    = 3. 认证
    = 4. 修改密码
==================================
 
7. 编写程序，从键盘获取一个人的信息，然后按照下面格式显示
> ==================================
    姓名: dongGe    
    QQ:xxxxxxx
    手机号:131xxxxxx
    公司地址:北京市xxxx
    ==================================
    
8. 编写程序，从键盘获取用户名和密码，然后判断，如果正确就输出以下信息 
> 亲爱的xxx，欢迎登陆 爱学习管理系统