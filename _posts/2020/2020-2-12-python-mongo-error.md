---
layout: post
title:  python mongo 错误处理：IndexError: no such item for Cursor instance
category: python
tags: Pythpn
keywords: Python mongo ndexError: no such item for Cursor instance
excerpt: "打印输出提示IndexError: no such item for Cursor instance，大意为：Cursor实例没有对应的节点，这样的问题该怎样解决呢？"
---


## 目录
### 错误信息

```
IndexError: no such item for Cursor instance
```

### 产生原因

```python
result = collection.find({"id": "12345678"})
print result[0]['name']
```
打印输出提示`IndexError: no such item for Cursor instance`，大意为：`Cursor实例没有对应的节点`，这样的问题该怎样解决呢？使用怎样的`if`判定可以判定结果集是有效地呢？


### 解决办法
`result.count()`

### 代码实例
```python
result = collection.find({"id": "12345678"})
if result and result.count():
    print result[0]['name']
```

