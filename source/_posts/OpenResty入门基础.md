---
title: OpenResty入门基础
tags:
- OpenResty
categories:
- 运维
date: 2018-11-14 18:35:08
---

## 安装

```shell
# 安装
$ sudo yum install pcre-devel openssl-devel gcc curl
$ wget https://openresty.org/download/openresty-1.13.6.2.tar.gz
$ tar -xzvf openresty-1.13.6.2.tar.gz
$ cd openresty-1.13.6.2
# 默认安装到/usr/local/openresty, 可以指定./configure --prefix=/your/path/openresty设置
# 默认安装的组件https://openresty.org/cn/components.html
$ ./configure --with-luajit \
            --without-http_redis2_module \
            --with-http_iconv_module \
            --with-http_postgres_module
$ make && sudo make install
# 创建软链接
$ ln -s /usr/local/openresty/nginx/sbin/nginx /usr/local/nginx
# 启动/重启/停止
$ nginx 
$ nginx -s reload
$ nginx -s stop
```

## 配置

> openResty的Nginx和普通Nginx配置一致