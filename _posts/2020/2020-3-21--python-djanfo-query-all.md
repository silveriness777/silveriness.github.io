---
layout: post
title: 'Django中对数据库mysql的all()查询的触发机制'
category: Python
tags: Python
keywords: Python 
excerpt: "Django 的 query 查询操作会一些缓存而且只有在做某些特殊操作的时候才会真正触发操作数据。"
---

## 目录

Django 的 query 查询操作会一些缓存而且只有在做某些特殊操作的时候才会真正触发操作数据。

具体操作包括：切片、取某个值、遍历等。


```
# 此时并不触发数据库
querys = Model.objects.all() 

# 不触发+1
b = querys.filter(filed1=xxx) 

# 触发
for i in querys:
    print i 
  
# 不触发，因为上个遍历时以缓存
for i in querys:
    print i 
   
 # 触发
list1 = querys[:2]
# 触发，切片并不缓存
list1 = query2[:2] 

# 触发
object1 = querys[2] 
# 触发，列表取值不缓存
object1 = querys[2] 
    
# 不触发，虽然此时querys已经缓存，但是链式查询是一个新的QuerySet对象，并不触发数据
c = querys.filter(filed1=xxx) 
```