---
title: ubuntu本地配置SSL
url: 199.html
id: 199
comments: false
categories:
  - 运维
date: 2017-11-07 11:25:08
tags:
---

    # 安装相关模块
    sudo a2enmod ssl   //启用ssl模块
    sudo apt-get install openssl  //安装openssl
    

    # 创建证书
    openssl genrsa -des3 -out server.key 1024  //创建CA签名(不使用密码去除-des3选项)
    openssl req -new -key server.key -out server.csr  //创建CSR(Certificate Signing Request)
    openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt  //自己签发证书
    sudo cp server.crt /etc/ssl/certs
    sudo cp server.key /etc/ssl/private
    

    # 修改虚拟主机信息
    在<VirtualHost *:80>段中，DocumentRoot一行的下方加入内容：
    SSLEngine On
    SSLOptions +StrictRequire
    SSLCertificateFile /etc/ssl/certs/server.crt
    SSLCertificateKeyFile /etc/ssl/private/server.key
    端口修改为：443，即<VirtualHost *:443>(ssl的端口)
    

    # 重启apache2
    sudo /etc/init.d/apache2 force-reload
    sudo /etc/init.d/apache2 restart