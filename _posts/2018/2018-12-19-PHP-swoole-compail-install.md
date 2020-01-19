---
layout: post
title: php编译安装扩展redis及swoole
category: PHP 
tags: [PHP]
---

## 安装redis扩展

### 下载redis扩展包以及解压

```
wget https://github.com/edtechd/phpredis/archive/php7.zip
unzip php7.zip
```
### 进入解压后目录，编译安装

```
cd data/pkg/phpredis-php7
/usr/local/php/bin/phpize
```
### 出现问题
```
Cannot find autoconf. Please check your autoconf installation and the
$PHP_AUTOCONF environment variable. Then, rerun this script.

#安装autoconf
yum install autoconf
```
### 开始编译裤子韩

```
/usr/local/php/bin/phpize
```
### 查找php-config
```
find / -name php-config
```
### 配置环境

```
./configure --with-php-config=/usr/local/php/bin/php-config
```
### 编译安装

```
make & make install
```
### 配置php.ini，添加redis.so扩展

```
vim /usr/local/php/etc/php.ini

extension=redis.so
```
### 重启php-fpm
```
killall php-fpm
/usr/local/sbin/php-fpm
```

### 2.安装swoole扩展

### 克隆swoole
```
git clone https://gitee.com/swoole/swoole.git 
```
### 安装swoole,且编译

```
cd swoole
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install
```
### 配置php.ini，添加swoole.so扩展

```
vim /usr/local/php/etc/php.ini

extension=swoole.so
```
### 重启php-fpm
```
killall php-fpm
/usr/local/sbin/php-fpm
```

更多精彩内容，请关注作者博客，[https://studytime.xin](https://studytime.xin/)




