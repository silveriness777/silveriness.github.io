---
layout: post
title:  requirements.txt文件的安装和使用
category: python-base 
tags: Pythpn 
keywords: Python requirements使用
excerpt: "在 Python 项目中 requirements.txt文件，记录了当前程序所有依赖包以及对应的版本号。它可以保证项目依赖包版本的确定性, 不会因为依赖更新而导致异常产生"
---

## 目录


### 说明
在 Python 项目中 requirements.txt文件，记录了当前程序所有依赖包以及对应的版本号。它可以保证项目依赖包版本的确定性, 不会因为依赖更新而导致异常产生.

### 生成requirements.txt文件
```python
pip freeze > requirements.txt
```


### 安装requirements.txt依赖
```python
pip install -r requirements.txt
```

### 列出已安装的包
```python
pip freeze or pip list
```

### 升级pip
```python
pip install -U pip
```
