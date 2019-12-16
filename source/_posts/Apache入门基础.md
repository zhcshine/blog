---
title: Apache入门基础
tags:
- Apache
categories:
- 运维
date: 2016-11-09 14:46:39
---

## Apache配置
### 查看Apache加载的模块
```
$ apachectl -t -D DUMP_MODULES  
```

   
### 禁用和启用模块
```shell
$ sudo a2dismod python
$ sudo a2enmod python
```

### 查看版本号

```shell
$ apachectl -v
```
   
### 赋予权限
 
```
# 2.2.x以下版本
Order deny,allow
Allow from all

# 2.4.x以上版本
Require all granted
```

