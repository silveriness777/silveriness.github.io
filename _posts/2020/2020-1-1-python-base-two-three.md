---
layout: post
title:  Python基础(二)：数据类型-tuple元组
category: python-base
tags: Pythpn 
keywords: Python 
excerpt: "tuple元组可以理解为元素值不变的list"
---
## 元组

### 元组是什么
Python的元组与列表类似，不同之处在于元组的元素不能修改。元组使用小括号，列表使用方括号。

### 元组定义
```
aTuple = ('et',77,99.9)
```

### 访问元组
`print(aTuple[0])`


### 修改元组
`Tuple[0]=100`
![](https://static.studytime.xin/image/articles/20200112021234.png)

说明： python中不允许修改元组的数据，包括不能删除其中的元素。

### 元组的内置函数count, index
index和count与字符串和列表中的用法相同
```
>>> a = ('a', 'b', 'c', 'a', 'b')
>>> a.index('a', 1, 3) # 注意是左闭右开区间
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: tuple.index(x): x not in tuple
>>> a.index('a', 1, 4)
3
>>> a.count('b')
2
>>> a.count('d')
0
```