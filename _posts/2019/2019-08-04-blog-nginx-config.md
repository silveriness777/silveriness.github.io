---
layout: post
title:  搭建个人博客（三）:个人博客nginx配置优化
category: Blog 
tags: blog nginx 建站 
---

近日发现博客域名studytime.xin和www.studytime.xin都是可以允许访问到的。导致使用卜算子统计访问信息时，数据也是分散的。针对于此，将域名所有请求统一转发到https://www.studytime.xin

## 个人博客系统nginx配置

### 配置https
> [阿里云申请配置https](https://www.studytime.xin/blog/2019/01/02/Linux-config-HTTPs.html)

### nginx配置

```
server {
  listen        80;
  server_name   studytime.xin;
  return 301    https://www.studytime.xin/$request_uri;
}
server {
    listen 443;
    server_name studytime.xin;
    return 301 https://www.studytime.xin$request_uri;
}

server {
  listen 443 default_server ssl;
  server_name  www.studytime.xin;
  ssl          on;
  root /data/wwwroot/blog;
  access_log  /data/log/nginx/studytime/access.log  main;
  error_log /data/log/nginx/studytime/error.log;
  index index.html;
  gzip on;
  gzip_min_length 1k;
  gzip_comp_level 2;
  gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png font/ttf font/otf image/svg+xml;
  gzip_vary on;
  gzip_disable "MSIE [1-6]\.";

  location ~* \.js$ {
    add_header "section" "long expire"; #  仅供说明
    add_header Cache-Control "max-age=31536000";
  }
   error_page  404 403 500 502 503 504  /404.html;

  location ~* \.js$ {
   add_header "section" "no etags"; #  仅供说明
   etag off;
  }
  ssl_certificate  /usr/local/nginx/ssl/studytime/1650160_studytime.xin.pem;
  ssl_certificate_key  /usr/local/nginx/ssl/studytime/1650160_studytime.xin.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
  ssl_prefer_server_ciphers on;
}

```
### nginx配置讲解

1、配置域名为`https://www.studytime.xin`

```
server {
  listen 443 default_server ssl;
  server_name  www.studytime.xin;
  ssl          on;
  root /data/wwwroot/blog;
  access_log  /data/log/nginx/studytime/access.log  main;
  error_log /data/log/nginx/studytime/error.log;
  index index.html;
  gzip on;
  gzip_min_length 1k;
  gzip_comp_level 2;
  gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png font/ttf font/otf image/svg+xml;
  gzip_vary on;
  gzip_disable "MSIE [1-6]\.";

  location ~* \.js$ {
    add_header "section" "long expire"; #  仅供说明
    add_header Cache-Control "max-age=31536000";
  }
   error_page  404 403 500 502 503 504  /404.html;

  location ~* \.js$ {
   add_header "section" "no etags"; #  仅供说明
   etag off;
  }
  ssl_certificate  /usr/local/nginx/ssl/studytime/1650160_studytime.xin.pem;
  ssl_certificate_key  /usr/local/nginx/ssl/studytime/1650160_studytime.xin.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
  ssl_prefer_server_ciphers on;
}

```

2、将所有不带www的请求，强制跳转www。也就是将studytime.xin的所有请求，包含http以及https转发到https://www.studytime.xin上。

```
server {
  listen        80;
  server_name   studytime.xin;
  return 301    https://www.studytime.xin/$request_uri;
}
server {
    listen 443;
    server_name studytime.xin;
    return 301 https://www.studytime.xin$request_uri;
}
```



