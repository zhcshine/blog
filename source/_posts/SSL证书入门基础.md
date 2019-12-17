---
title: SSL证书入门基础
categories:
- 运维
date: 2017-11-07 11:25:08
tags:
- SSL
---

## 原理

### CA
现实中，通过CA（Certificate Authority）来保证public key的真实性。CA也是基于非对称加密算法来工作。有了CA，B会先把自己的public key（和一些其他信息）交给CA。CA用自己的private key加密这些数据，加密完的数据称为B的数字证书。现在B要向A传递public key，B传递的是CA加密之后的数字证书。A收到以后，会通过CA发布的CA证书（包含了CA的public key），来解密B的数字证书，从而获得B的public key。

但是等等，A怎么确保CA证书不被劫持。C完全可以把一个假的CA证书发给A，进而欺骗A。CA的大杀器就是，CA把自己的CA证书集成在了浏览器和操作系统里面。A拿到浏览器或者操作系统的时候，已经有了CA证书，没有必要通过网络获取，那自然也不存在劫持的问题。

CA作为一个公证机构，能确保数字证书的真实性。但是在实际使用中，CA认证一般是要收费的，普通人不会去做CA认证，进而获得属于自己的数字证书。更多的是，一些大的机构，例如银行，网店，金融机构，它们去获得自己的数字证书。那这种情况如何保证网络通信的安全呢？

这些机构获取到CA授予的数字证书之后，将数字证书加到自己的web服务器上。

### HTTPS
> HTTPS全称是HTTP over SSL，也就是通过SSL/TLS加密HTTP数据，这或许是SSL最广泛的应用。

**HTTPS流程:**

1. 用户向web服务器发起一个安全连接的请求
2. 服务器返回经过CA认证的数字证书，证书里面包含了服务器的public key
3. 用户拿到数字证书，用自己浏览器内置的CA证书解密得到服务器的public key
4. 用户用服务器的public key加密一个用于接下来的对称加密算法的密钥，传给web服务器
5. 因为只有服务器有private key可以解密，所以不用担心中间人拦截这个加密的密钥
6. 服务器拿到这个加密的密钥，解密获取密钥，再使用对称加密算法，和用户完成接下来的网络通信

## 安装

### Ubuntu安装
```shell
# 安装相关模块
$ sudo a2enmod ssl   //启用ssl模块
$ sudo apt-get install openssl  //安装openssl
```

### 创建证书
```shell
openssl genrsa -des3 -out server.key 1024  //创建CA签名(不使用密码去除-des3选项)
openssl req -new -key server.key -out server.csr  //创建CSR(Certificate Signing Request)
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt  //自己签发证书
sudo cp server.crt /etc/ssl/certs
sudo cp server.key /etc/ssl/private
```

### 虚拟主机

```shell
# 修改虚拟主机信息
# 在<VirtualHost *:80>段中，DocumentRoot一行的下方加入内容：
SSLEngine On
SSLOptions +StrictRequire
SSLCertificateFile /etc/ssl/certs/server.crt
SSLCertificateKeyFile /etc/ssl/private/server.key
# 端口修改为：443，即<VirtualHost *:443>(ssl的端口)
```

重启apache2
```shell
$ sudo /etc/init.d/apache2 force-reload
$ sudo /etc/init.d/apache2 restart
```
