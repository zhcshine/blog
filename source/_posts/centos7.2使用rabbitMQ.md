---
title: centos7.2使用rabbitMQ
url: 549.html
id: 549
comments: false
categories:
  - 后端
  - 运维
date: 2018-09-08 14:45:54
tags:
---

    # 安装 Erlang
    yum -y install epel-release
    yum -y update
    yum -y install erlang socat
    

    # 查看是否安装成功
    erl -version
    

    # 安装rabbitMQ
    wget https://www.rabbitmq.com/releases/rabbitmq-server/v3.6.10/rabbitmq-server-3.6.10-1.el7.noarch.rpm
    rpm --import https://www.rabbitmq.com/rabbitmq-release-signing-key.asc
    rpm -Uvh rabbitmq-server-3.6.10-1.el7.noarch.rpm
    

    # 启用,开机启动,状态
    systemctl start rabbitmq-server
    systemctl enable rabbitmq-server
    systemctl status rabbitmq-server
    

    # 启动图形界面管理
    rabbitmq-plugins enable rabbitmq_management
    chown -R rabbitmq:rabbitmq /var/lib/rabbitmq/
    rabbitmqctl add_user admin StrongPassword  # 踩坑:密码不要以';'结尾
    rabbitmqctl set_user_tags admin administrator
    rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
    

    # 自定义配置文件
    cd /etc/rabbitmq
    touch rabbitmq.conf
    

    # 访问图形界面
    http://Your_Server_IP:15672/
    

    # api文档
    http://Your_Server_IP:15672/api