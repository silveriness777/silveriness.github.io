---
layout: post
title:  Python基础(二)：1. 数据类型-字符串
category: Python 
tags: Pythpn 
keywords: Python 
excerpt: "双引号或者单引号中的数据，就是字符串。"
---

## 目录
## 字符串

### 字符串是什么？
双引号或者单引号中的数据，就是字符串。
```
message1 = 'hello studytime.xin'
message2 = "hello studytime.xin"
```

### 字符串输出
```
name = 'da bai'
position = 'Python 工程师'
address = '深圳市'
print('--------------------------------------------------')
print("姓名：%s" % name)
print("职位：%s" % position)
print("公司地址：%s" % address)
print('--------------------------------------------------')
```

### 字符串输入
input()

### 字符串下标
列表与元组⽀持下标索引好理解，字符串实际上就是字符的数组，所以也⽀持下标索引

```
name = 'da bai'
print(name[0])
print(name[1])
print(name[2])
```


### 字符串切片
切⽚是指对操作的对象截取其中⼀部分的操作。字符串、列表、元组都⽀持 切⽚操作。
切⽚的语法：[起始:结束:步⻓]

```
# 特殊说明 结束字符指定了的代表到指定的前一个
name = "hello studytime.xin"
print(name[0:5])  # 取下标为0~4 的字符
print(name[3:5])  # 取下标为3、4 的字符
print(name[2:])  # 取下标为2开始到最后的字符
print(name[1:-1])  # 取下标为1开始到最后第2个之间的字符
print('从倒数第2个下标到尾部' + name[-1:])  # 取倒数第二个字符到最后的字符
```

### 字符串常见操作
```
mystr = "hello world php and python"
```

#### 字符串长度
```
print(len(mystr))
```

#### find,检测 str 是否包含在 mystr中，如果是返回开始的索引值，否则返回-1
```
print(mystr.find('python'))
print(mystr.find('python', 0, 15))
```

#### index,跟find()⽅法⼀样，只不过如果str不在 mystr中会报⼀个异常,可以指定区间，也可以不指定
```
print(mystr.index('python', 0, 15))
```

#### count 返回 str在start和end之间 在 mystr⾥⾯出现的次数,可以指定区间，也可以不指定
```
mystr.count('python', 0, 15)
```

#### replace， 把 mystr 中的 str1 替换成 str2,如果 count 指定，则替换不超过 count 次.
```
mystr.replace(str1, str2, mystr.count(str1))
mystr.replace('python', 'python1')
```

#### split,以 str 为分隔符切⽚ mystr，如果 maxsplit有指定值，则仅分隔 maxsplit 个⼦ 字符串
`mystr.split(" ", 2)`

#### capitalize,把字符串的第⼀个字符⼤写
`mystr.capitalize()`

#### title,把字符串的每个单词⾸字⺟⼤写
`mystr.title()`

#### startswith,检查字符串是否是以 obj 开头, 是则返回 True，否则返回 False
`mystr.startswith('P')`

#### endswith,检查字符串是否以obj结束，如果是返回True,否则返回 False.
`mystr.endswith('P')`