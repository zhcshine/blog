---
title: zabbix安装及使用
url: 615.html
id: 615
comments: false
categories:
  - 后端
date: 2018-11-16 17:40:04
tags:
---

#### 安装服务器端

[官方](https://www.zabbix.com/download) 1\. 根据实际情况选择数据库 2. 根据实际情况选择操作系统

#### 汉化

![](/wp-content/uploads/2018/11/2018-11-16-17-36-53屏幕截图.png) 错误提示

> You are not able to choose some of the languages, because locales for them are not installed on the web server.

    # 选中zh-CN UTF-8
    sudo dpkg-reconfigure locales
    sudo service apache2 restart
    

#### 安装客户端

[官方](https://www.zabbix.com/documentation/3.2/manual/installation/install_from_packages/agent_installation) [参考](https://tecadmin.net/install-zabbix-agent-on-centos-rhel/)

#### 设置邮件报警

Media types ![](/wp-content/uploads/2018/11/zabbix-email.png) ![](/wp-content/uploads/2018/11/zabbix-email-1.png)

#### 交换分区错误问题

    Lack of free swap space
    

现在的云主机默认没有交换分区 Configuration->Templates->Template OS Linux->Triggers->Lack of free swap space on {HOST.NAME}->Expression

    {Template OS Linux:system.swap.size[,pfree].last(0)}<50改成
    {Template OS Linux:system.swap.size[,pfree].last(0)}<50 and {Template OS Linux:system.swap.size[,free].last(0)}<>0