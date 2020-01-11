---
layout: post
title: Python基础(二)：数据类型-dict字典
category: Python 
tags: Pythpn 
keywords: Python 
excerpt: "字典，是一系列的键值对"
---
## 字典

### 字典，是一系列的键值对
`info = {'name': 'da bai', 'age': 26, 'site': 'studytime.xin'}`

### 访问字典中的值,若键值不存在则报错
```
print(info['name'])
print(info.get('age'))
```

### 修改元素
`info['age'] = 'da bai2'`

### 添加元素
#### 存在即为修改，不存在则为新增
`info['sex'] = 'man'`

### 删除元素
#### del 指定下标则删除对应下标的值，不指定则删除整个字典
```
del info['sex']
del info
```

### clear清空整个字典
```
info = {'name': 'da bai', 'age': 26, 'site': 'studytime.xin'}
info.clear()
```

### 其他常见操作
```
info = {'name': 'da bai', 'age': 26, 'site': 'studytime.xin'}
print(len(info))
```

### 返回⼀个包含字典所有key的列表
`info.keys()`

### 返回⼀个包含字典所有value的列表
`info.values()`

### items 返回⼀个包含所有（键，值）元祖的列表
`info.items()`

### has_key 如果key在字典中，返回True，否则返回False
`info.has_key('name')`

### 字典遍历遍历

`info = {'name': 'da bai', 'age': 26, 'site': 'studytime.xin'}`
#### 遍历字典中的key
```
for key in info.keys():
    print(key)
    
```

#### 遍历字典的value（值）
```
for value in info.values():
    print(value)
```

#### 遍历字典的项
```
for item in info.items():
    print(item)
```

#### 遍历字典的key-value
```
for key, value in info.items():
    print(key, value)
```