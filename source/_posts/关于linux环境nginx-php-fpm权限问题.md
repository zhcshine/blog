---
title: 关于linux环境nginx/php-fpm权限问题
url: 576.html
id: 576
comments: false
categories:
  - 运维
date: 2018-11-10 13:10:28
tags:
---

#### 当前用户hanchang, nginx用户nginx

    vim /etc/nginx/nginx.conf
    user hanchang;
    

    vim /etc/php-fpm.d/www.conf 
    user = hanchang
    group = nginx
    listen.owner = hanchang
    listen.group = nginx
    listen.mode = 0660
    

#### 划重点 将当前用户加入nginx用户组

    sudo usermod -a -G nginx hanchang