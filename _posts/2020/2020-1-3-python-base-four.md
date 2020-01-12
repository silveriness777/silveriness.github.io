---
layout: post
title:  Python基础(四)：函数
category: python-base 
tags: Pythpn 
keywords: Python 
excerpt: "为了提高编写的效率以及代码的重用，把具有独立功能的代码块组织为一个小模块，这就是函数。"
---

## 目录
### 函数是什么？
为了提高编写的效率以及代码的重用，把具有独立功能的代码块组织为一个小模块，这就是函数。

### 函数定义、格式
```
 def 函数名():
        代码
        
        
def printInfo():
    print('------------------------------------')
    print('         人生苦短，我用Python')
    print('------------------------------------')        
```

### 函数的调用
通过 函数名() 即可完成调用
`printInfo()`
           
### 定义带有参数的函数
```
def add2num(a, b):
    c = a + b
    print(c)

add2num(1, 3)
```

### 形参、实参概念
定义时小括号中的参数，用来接收参数用的，称为 形参
调用时小括号中的参数，用来传递给函数用的，称为 实参

### 缺省参数
调用函数时，缺省参数的值如果没有传入，则被认为是默认值
```
def printinfo(name, age=35):
    print("Name: ", name)
    print("Age ", age)


printinfo(name="dabai")
printinfo(age=18, name="dabai")  # 报错
# 注意：带有默认值的参数一定要位于参数列表的最后面。
```

### 不定长参数
有时可能需要一个函数能处理比当初声明时更多的参数。这些参数叫做不定长参数，声明时不会命名。
基本格式：
```
def functionname([formal_args,] *args, **kwargs):
       "函数_文档字符串"
       function_suite
       return [expression]
```
特殊说明：
加了星号（*）的变量args会存放所有未命名的变量参数，args为元组
加**的变量kwargs会存放命名参数，即形如key=value的参数， kwargs为字典

```
def fun(a, b, *args, **kwargs):
    print("a =", a)
    print("b =", b)
    print("args =", args)
    print("kwargs =")
    for key, value in kwargs.items():
        print(key, "=", value)


fun(1, 2, 3, 4, 5, m=6, n=7, p=8)
```

### 函数返回值
程序中函数完成一件事情后，最后给调用者返回的结果。 return实现
```
def add2num(a, b):
    return a + b

result = add2num(100, 98)
print(result)
```

### 多个返回值 实际上是利用元组概念
```
def divid(a, b):
    shang = a // b
    yushu = a % b
    return shang, yushu


sh, yu = divid(5, 2)
printInfo(sh)
```

### 递归函数
如果一个函数在内部不调用其它的函数，而是自己本身的话，这个函数就是递归函数。
```
def test5(num):
    if num > 1:
        result = num * test5(num - 1)
    else:
        result = 1
    return result
result = test5(5)
print(result)
```

### 匿名函数
用lambda关键词能创建小型匿名函数。这种函数得名于省略了用def声明函数的标准步骤。
```
格式：
lambda [arg1 [,arg2,.....argn]]:expression

sum = lambda arg1, arg2: arg1 + arg2

print("Value of total : ", sum( 10, 20 ))
print("Value of total : ", sum( 20, 20 ))
```

### 函数的四种类型
函数根据有没有参数，有没有返回值，可以相互组合，一共有4种
- 无参数，无返回值
- 无参数，又反悔
- 有参数，无返回值
- 有参数，有返回值

#### 无参数，无返回值的函数
```
def printMenu():
    print('--------------------------')
    print('      xx涮涮锅 点菜系统')
    print('')
    print('  1.  羊肉涮涮锅')
    print('  2.  牛肉涮涮锅')
    print('  3.  猪肉涮涮锅')
    print('--------------------------')
```


#### 无参数，有返回值的函数
```
def getTemperature():
    return 24

temperature = getTemperature()
print('当前的温度为:%d' % temperature)
```

### 有参数，无返回值的函数 一般情况下，对某些变量设置数据而不需结果时，用此类函数

### 有参数，有返回值的函数
```
def calculateNum(num):
    result = 0
    i = 1
    while i <= num:
        result = result + i

        i += 1

    return result

result = calculateNum(100)
print('1~100的累积和为:%d' % result)
```


### 作用域

### 什么是局部变量
局部变量，就是在函数内部定义的变量
不同的函数，可以定义相同的名字的局部变量，但是各用个的不会产生影响
局部变量的作用，为了临时保存数据需要在函数中定义变量来进行存储，这就是它的作用

### 全局变量
既能在一个函数中使用，也能在其他的函数中使用，这样的变量就是全局变量
```
a = 100
def test1():
    print(a)

def test2():
    print(a)

# 调用函数
test1()
test2()
```

### 全局变量和局部变量名字相同问题 局部变量优先级

### 修改全局变量
```
def test3():
    global a
    print('修改前a值为' + a)
    a = 200
    print('修改后a值为' + a)

test3()
```
