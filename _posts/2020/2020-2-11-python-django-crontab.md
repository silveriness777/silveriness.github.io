---
layout: post
title:  python模块：django-crontab实现服务端的定时任务
category: python
tags: Pythpn itsdangerous
keywords: Python itsdangerous
excerpt: "公司 Django 应用中需要添加定时任务来定时执行一些数据清洗的功能，因此想到了使用 django-crontab 插件可以满足我的需求，下面就是这个插件的使用方法。"
---

## 目录

### 安装
`
pip install django-crontab
`
### 在Django项目中使用
```python
settings.py

INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'django_crontab'
)
```


### 配置任务
```python
CRONJOBS=(
    # 每一分钟执行一次你的定时函数
    ('*/ * * * *', 'appname.cron.test'),
    # 定时函数输出的内容到指定文件（如果该路径或文件不存在将会自动创建）
    ('0  0 1 * *', 'app名.定时函数所在文件名.定时函数名', '>输出文件路径和名称'),
    # 在12点10分执行命令
    ('10 12 * * *', 'django.core.management.call_command', ['要执行的命令']),
)
```

### 在app中（与views.py同级）新建cron.py文件（文件名不限定）
```python
def test():
    print('django_crontab test')
```

### 使用操作

```
python manage.py crontab add　　添加定时任务
python manage.py crontab remove 清除定时任务
python manage.py crontab show   显示定时任务
```

### 扩展知识

```
前5个（*）星号

M(分钟0-59)
H(小时0-23)
D(天1-31)
m(月1-12)
d(一星期0-6,0为星期天)

特殊符号

* 代表所有的取值范围的数字
/ 代表每的意思，*/5就是每5个单位
- 代表从某个数字到某个数字
, 分开几个离散的数字

eg：
0 14 * * 1-5 周一到周五每天下午两点触发任务
0 10 * * 0,6 周六、周天早上十点触发任务
*/5 9-10 * * 1-5 周一到周五九点到十点每五分钟触发任务
```

### 日志打印

```python
  ('*/ * * * *', 'appname.cron.test','> appname.txt'),
```
