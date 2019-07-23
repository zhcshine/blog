---
title: Centos安装s_h_a_d_o_w_s_o_c_k_s服务
tags:
  - linux
url: 31.html
id: 31
comments: false
categories:
  - 运维
date: 2016-11-09 10:22:52
---

### Python版本

    python –version
    

CentOS6.5默认安装的Python版本是2.6.6，返回值为：Python 2.6.6

### 安装setuptools

    yum install -y python-setuptools
    

安装完毕后，easy_install命令就可以使用了。

### 安装pip

easy\_install pip 通过easy\_install安装pip是最为简单的方法。pip默认安装到/usr/bin目录下。

### 安装s\_h\_a\_d\_o\_w\_s\_o\_c\_k\_s

    pip install shadowsocks
    

### s\_h\_a\_d\_o\_w\_s\_o\_c\_k\_s的配置和使用

至于s\_h\_a\_d\_o\_w\_s\_o\_c\_k\_s可以拿来做什么，这里就毋须赘述了。直接开始吧。

### 创建s\_h\_a\_d\_o\_w\_s\_o\_c\_k\_ss的配置文件：

CentOS下通过vim创建该配置，运行命令： vi /etc/shadowsocks.json

    {
    "server":"your_server_ip",
    "server_port":8989,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"yourpassword",
    "timeout":600,
    "method":"aes-256-cfb",
    "fast_open": false,
    "workers": 1
    }
    

各字段的含义： server：服务器 IP (IPv4/IPv6)，注意这也将是服务端监听的 IP 地址  
server_port：监听的服务器端口  
local_address：本地监听的 IP 地址  
local_port：本地端端口  
password：用来加密的密码  
timeout：超时时间（秒）  
method：加密方法，可选择 “bf-cfb”, “aes-256-cfb”, “des-cfb”, “rc4″, 等等。默认是一种不安全的加密，推荐用 “aes-256-cfb” fast\_open：true 或 false。如果你的服务器 Linux 内核在3.7+，可以开启 fast\_open 以降低延迟。开启方法： echo 3 > /proc/sys/net/ipv4/tcp_fastopen  
开启之后，将 fast_open 的配置设置为 true 即可。  
works：works数量，默认为 1

### 安装M2Crypto

默认加密方法 table 速度很快，但很不安全。推荐使用 “aes-256-cfb” 或者 “bf-cfb”。请不要使用 “rc4″，它不安全。如果选择 “table” 之外的加密，需要安装 M2Crypto。 先安装依赖包：

    yum install -y openssl-devel gcc swig python-devel autoconf libtool
    

### 安装setuptools

    wget --no-check-certificate https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py
    python ez_setup.py install
    

再通过pip安装M2Crypto：

    pip install M2Crypto
    

### 安装 gevent

安装 gevent可以提高 Shadowsocks 的性能。CentOS下安装gevent依赖libevent和greenlet。

### 安装libevent

    yum install -y libevent
    

### greenlet：

    pip install greenlet
    

###安装gevent：

    pip install gevent
    

### 命令行参数（服务器端启动命令）

    ssserver -c /etc/shadowsocks.json
    

如果想在后台一直运行Shadowsocks，启动命令如下：

    nohup ssserver -c /etc/shadowsocks.json > /dev/null 2>&1 &
    

备注：关于nohup，是可以让程序在后台运行的命令。 同时可以用命令行参数覆盖 /etc/shadowsocks.json 里的设置：

    sslocal -s 服务器地址 -p 服务器端口 -l 本地端端口 -k 密码 -m 加密方法
    ssserver -p 服务器端口 -k 密码 -m 加密方法
    

备注：sslocal是客户端程序；ssserver是服务端程序。

### 防火墙设置（如有）

编辑防火墙配置文件/etc/sysconfig/iptables，将服务器端口（server_port）放行。  
新增一条防火墙规则：

    iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 8989 -j ACCEPT
    

先要保存下:

    service iptables save
    

重启防火墙iptables：

    service iptables restart
    

至此，服务器端的 Shadowsocks 安装和配置完毕。 配置多个用户

    {
    "timeout": 600,
    "method": "aes-256-cfb",
    "port_password":
    {
    "40001": "password1",
    "40002": "password2",
    "40003": "password3"
    },
    "_comment":
    {
    "40001": "xiaoming",
    "40002": "lilei",
    "40003": "mike"
    }
    }