---
title: Zabbix入门基础
categories:
- 后端
date: 2018-11-16 17:40:04
tags:
- Zabbix
---

## 安装

### 服务器端

[官方](https://www.zabbix.com/download) 

1. 根据实际情况选择数据库 
2. 根据实际情况选择操作系统

汉化

![](/wp-content/uploads/2018/11/2018-11-16-17-36-53屏幕截图.png) 

错误提示

<font color="#d44375">You are not able to choose some of the languages, because locales for them are not installed on the web server.</font>
```shell
# 选中zh-CN UTF-8
$ sudo dpkg-reconfigure locales
$ sudo service apache2 restart
```

### 安装客户端

[官方](https://www.zabbix.com/documentation/3.2/manual/installation/install_from_packages/agent_installation) 

[参考](https://tecadmin.net/install-zabbix-agent-on-centos-rhel/)

### 交换分区错误问题

<font color="#d44375">Lack of free swap space</font>

现在的云主机默认没有交换分区 Configuration->Templates->Template OS Linux->Triggers->Lack of free swap space on {HOST.NAME}->Expression

```shell
{Template OS Linux:system.swap.size[,pfree].last(0)}<50改成
```
```shell
{Template OS Linux:system.swap.size[,pfree].last(0)}<50 and {Template OS Linux:system.swap.size[,free].last(0)}<>0
```
