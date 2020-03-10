---
layout: post
title: 'mac环境 下MySQL忘记密码后重置密码'
category: mysqsl
tags: mysql
keywords: mysql mac忘记密码
excerpt: "mac下mysql忘记root用户密码，怎么进行重置？"
---

## 目录

### 问题
mac下mysql忘记root用户密码，怎么进行重置？

### 具体流程
通过以下4步就可重新设置新密码：

1. 停止 mysql server，通常是在 '系统偏好设置' > MySQL > 'Stop MySQL Server'
2. 打开命令行输入：`sudo /usr/local/mysql/bin/mysqld_safe --skip-grant-tables`
3. 打开另一个新终端：`sudo /usr/local/mysql/bin/mysql -u root`
4. 重置密码
```
UPDATE mysql.user SET authentication_string=PASSWORD('新密码') WHERE User='root';
FLUSH PRIVILEGES;
```
5. 重启MySQL即可

### 特殊说明
以上方法针对 mysql V5.7.9, 旧版的mysql请使用：
`UPDATE mysql.user SET Password=PASSWORD('新密码') WHERE User='root';`