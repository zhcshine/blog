---
title: Nagios监控集群服务
tags:
- Nagios
categories:
- 运维
date: 2017-04-20 11:17:12
---

## 安装服务器端

### 安装lamp环境

{% post_link Centos7服务器基础配置 %}

### 配置nagios服务

```shell
# 192.168.0.206
# 创建nagiso用户
$ sudo useradd nagios
# 创建apache用户，yum安装httpd服务会自动创建
$ sudo useradd apache -M -s /sbin/nologin
useradd：用户“apache”已存在
# 创建nagcmd用户组
$ sudo groupadd nagcmd
# 将usermod加入nagcmd用户组
sudo usermod -a -G nagcmd nagios
# 将apache用户加入nagcmd用户组
$ sudo usermod -a -G nagcmd apache
# 查看nagios用户所属的组
$ id -n -G nagios
nagios nagcmd
# 查看apace用户所属的组
$ id -n -G apache
apache nagcmd
# 安装nagiso服务
$sudo yum install nagios
$sudo yum install nagios-plugins-all
# 安装nrpe（服务器端和客户端都需要安装）
$sudo yum install nrpe
# 启动和开机启动
$sudo systemctl start nagios
$sudo systemctl enable nagios
$sudo systemctl start nrpe
$sudo systemctl enable nrpe
```
 
### 配置web

```shell
# 取消认证
$ sudo vim /etc/nagios/cgi.cfg
use_authentication=0 # 此处将1改为0
# 查看配置文件
$ls /etc/httpd/conf.d
autoindex.conf  nagios.conf  php.conf  README  userdir.conf  welcome.conf  xcache.conf
# 其中nagiso.conf就是nagios的web配置文件
# 访问http://192.168.0.206/nagios，其中登陆密码在/etc/nagios/passwd文件中，具体文件位置可以在/etc/httpd/conf.d/nagios.conf中查看
```
    
## 安装客户端

```shell
# 192.168.0.208
# 安装mysql，参考上方链接，配合生成check_mysql插件
# 安装nrpe（服务器端和客户端都需要安装）
$ sudo yum -y install epel-release
$ sudo yum install nrpe
$ sudo yum install nagios-plugins-all
# 配置nagios客户端
$ sudo vim /etc/nagios/nrpe.cfg
allowed_hosts=127.0.0.1,192.168.0.206 # 监控当前客户端的nagios服务器地址
# 启动服务
$ sudo systemctl restart nrpe
$ sudo systemctl enable nrpe
# 检查启动结果
$ sudo netstat -lntup | grep nrpe
tcp        0      0 0.0.0.0:5666            0.0.0.0:*               LISTEN      2540/nrpe           
tcp6       0      0 :::5666                 :::*                    LISTEN      2540/nrpe
$ sudo ps -ef | grep nrpe | grep -v grep
nrpe      2540     1  0 11:59 ?        00:00:00 /usr/sbin/nrpe -c /etc/nagios/nrpe.cfg -d

```
    
## 配置服务器端

```shell
# 192.168.0.206
$ tree /etc/nagios
.
├── cgi.cfg  
├── conf.d  # 添加新的监视客户端信息
│   └── web_one.cfg
├── nagios.cfg  # 配置服务器端nagios
├── nrpe.cfg # 配置服务器端nrpe
├── objects
│   ├── commands.cfg
│   ├── contacts.cfg
│   ├── localhost.cfg
│   ├── printer.cfg
│   ├── switch.cfg
│   ├── templates.cfg
│   ├── timeperiods.cfg
│   └── windows.cfg
├── passwd
└── private
    └── resource.cfg

$ cp /etc/nagios/objects/localhost.cfg  /etc/nagios/conf.d/web_one.cfg
$ vim /etc/nagios/conf.d/web_one.cfg # 将localhost 改成web_one
define host{
        use                     linux-server  
        host_name         web_one
        alias                   web_one
        address              192.168.0.208
}
define service{
        use                             local-service 
        host_name                 web_one
        service_description    PING
        check_command        check_ping!100.0,20%!500.0,60%
}
```
