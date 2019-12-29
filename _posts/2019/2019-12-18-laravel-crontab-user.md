---
layout: post
title: Laravel定时脚本root运行日志权限问题错误解决
category: Laravel 
tags: [Laravel]
---

---
layout: post
title:  Laravel定时脚本root运行日志权限问题错误解决
category: Laravel 
tags: Laravel 
keywords: Laravel
excerpt: "记录一次laravel任务调度中crontab运行用户错误导致的权限错误问题"
---

## 目录

### 问题描述
我们在部署 web 服务时，经常用到定时任务。正常的流程就是直接执行下面的指令来配置定时任务，crontab -e 。问题来了，正常情况下，这个指令在哪个用户下面执行，这个权限就是谁的，我们公司的是使用 root。
而一般的 运行php都会创建单独的用户，www 或者php-fpm来运行。

假如定时任务出错了，就会记录错误日志到 storage/logs 里了，一般就是 laravel.log 文件。而这时 laravel.log 所属者就是 root 了。
用户通过浏览器访问我们网站，如果出错，以 www 身份尝试记录到 storage/logs/laravel.log，然后写不进去，就会一直报错！

### 解决办法
crontab 命令可以使用 -u 参数指定运行脚本的用户。
```
crontab -u www -e
* * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
```