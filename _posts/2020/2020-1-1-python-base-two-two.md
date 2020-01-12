---
layout: post
title:  Python基础(二)：数据类型-list列表
category: python-base
tags: Pythpn 
keywords: Python 
excerpt: "list为python的基本元素之一。"
---

## list

### 定义列表
`namesList = ['PHP', 'Java', 'Python']`

### 打印元素
`print(namesList[0])`

### 循环遍历
```
for lan in namesList:
    print(lan)

i = 0
while i < len(namesList):
    print(namesList[i])

```

### 列表增加元素
#### append，尾部追加
bigData = ['Hadoop', 'Yarn', 'MapReduce']
print("-----添加之前，列表bigData的数据-----")
for tempName in bigData:
    print(tempName)

#### 提示、并添加元素
temp = input('请输⼊要添加的大数据组件:')
bigData.append(temp)
print("-----添加之后，列表A的数据-----")
for tempName in bigData:
    print(tempName)

#### extend 通过extend可以将另⼀个集合中的元素逐⼀添加到列表中
```
a = [1, 2]
b = [3, 4]
a.append(b)  # [1, 2, [3, 4]]
a.extend(b)  # [1, 2, [3, 4], 3, 4]
```

#### insert, 指定下标追加元素,原有元素下表依次后移
```
a = [0, 1, 2]
a.insert(1, 3)  # [0, 3, 1, 2]
```

### 修改元素
`bigData[0] = 'Flume'`

### 查找元素
#### in,not in
```
if 'Hadoop' in bigData:
    print('in')
else:
    print('not in')
```

#### index, count
```
bigData.index('Flume', 1, 3)
bigData.count('Flume')
```
### 删除元素
#### del：根据下标进⾏删除
`del bigData[0]`

#### pop：删除最后⼀个元素
`bigData.pop()`

#### remove：根据元素的值进⾏删除
`bigData.remove('Hadoop')`

### 排序
#### sort，sort⽅法是将list按特定顺序重新排列，默认为由⼩到⼤，参数reverse=True 可改为倒序，由⼤到⼩。
```
nums = [2, 1, 3, 4, 6, 4]
a.reverse()
a.sort()
a.sort(reverse=True)
```

### 嵌套
```
a = [1, 2, [1, 2]]
print(a[2][1])
```

### 实现列表带下标索引的遍历
```python
chars = ['a', 'b', 'c', 'd']
for key, num in enumerate(nums):
    print(key, num)
# 0 a
#
# 1 b
#
# 2 c
#
# 3 d
```