---
layout: post
title:  Python基础(三)：判断语句和循环语句
category: Python 
tags: Pythpn 
keywords: Python 
excerpt: "条件判断语句和循环语句都是编写代码中经常用到的语句"
---

## 目录

### if 判断语句

1. 简答if语句

格式：
```
 if condition:
        do something

```

demo:
```
age = 30
print('------if判断开始 - -----')
if age >= 18:
    print('已经成年啦')
print('------if判断结束------')
```

2. if-else语句

格式：
```
 if condition:
        满足条件时要做的事情
    else:
        不满足条件时要做的事情
```

demo：
```
ticket = 1
if ticket == 1:
    print("有车票，可以上火车")
    print("终于可以见到Ta了，美滋滋~~~")
else:
    print("没有车票，不能上车")
    print("亲爱的，那就下次见了，一票难求啊")
```

3. if-elif-else语句

格式：
```
    if condition1:
        满足条件1要做的事情
    elif condition2:
        满足条件2要做的事情
    else:
        不满足上述条件需要做的事情
```

demo:
```
score = 70
if score >= 90 and score <= 100:
    print('本次考试，等级为A')
elif score >= 80 and score < 90:
    print('本次考试，等级为B')
elif score >= 70 and score < 80:
    print('本次考试，等级为C')
elif score >= 60 and score < 70:
    print('本次考试，等级为D')
elif score >= 0 and score < 60:
    print('本次考试，等级为E')
else:
    print('未知')
```

4. 练习
```
import random

player = input('请输入：剪刀(0)  石头(1)  布(2):')
player = int(player)

computer = random.randint(0,2)

if ((player == 0) and (computer == 2)) or ((player == 1) and (computer == 0)) or ((player == 2) and (computer == 1)):
    print('获胜，哈哈，你太厉害了')
elif player == computer:
    print('平局，要不再来一局')
else:
    print('输了，不要走，洗洗手接着来，决战到天亮')


```

### for 循环
基本格式：
```
for循环的格式

for 临时变量 in 列表或者字符串等:
    循环满足条件时执行的代码
```

demo：
```
name = 'dabai'
for i in name:
    print(i)

```


### while 循环
for 循环用户针对集合中的每个元素的一个代码块，而while循环不断的执行，直到条件不满足为止。

基本格式：
```
while 判断条件 :
    执行任务语句
```

demo1:
```
i = 0
while i < 5:
    print("当前是第%d次执行循环" % (i + 1))
    print("i=%d" % i)
    i += 1
```


demo2:
```
i = 1
sum = 0
while i <= 100:
    sum = sum + i
    i += 1

print("1~100的累积和为:%d" % sum)

```


### break、continue

break 结束打断循环
continue 跳出当前循环，执行下次循环

demo1：
```
name = 'dabai'
for x in name:
    print('----')
    if x == 'a':
        break
    print(x)

i = 0
while i < 10:
    i = i + 1
    print('----')
    if i == 5:
        break
    print(i)

```

demo2：
```
name = 'dabai'

for x in name:
    print('----')
    if x == 'b':
        continue
    print(x)

i = 0
while i < 10:
    i = i + 1
    print('----')
    if i == 5:
        continue
    print(i)

```
