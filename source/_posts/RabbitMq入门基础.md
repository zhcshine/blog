---
title: RabbitMq入门基础
tags:
- RabbitMq
categories:
- 运维
date: 2018-09-08 14:45:54
---

## 安装

### 安装Erlang

```shell
# 安装 Erlang
$ yum -y install epel-release
$ yum -y update
$ yum -y install erlang socat
# 查看是否安装成功
$ erl -version
```

### 安装RabbitMq

```shell
# 安装rabbitMQ3.6版本
$ wget https://www.rabbitmq.com/releases/rabbitmq-server/v3.6.10/rabbitmq-server-3.6.10-1.el7.noarch.rpm
$ rpm --import https://www.rabbitmq.com/rabbitmq-release-signing-key.asc
$ rpm -Uvh rabbitmq-server-3.6.10-1.el7.noarch.rpm

# 启用,开机启动,状态
$ systemctl start rabbitmq-server
$ systemctl enable rabbitmq-server
$ systemctl status rabbitmq-server
```

### 图形界面

```shell
# 启动图形界面管理
rabbitmq-plugins enable rabbitmq_management
chown -R rabbitmq:rabbitmq /var/lib/rabbitmq/
rabbitmqctl add_user admin StrongPassword  # 踩坑:密码不要以';'结尾
rabbitmqctl set_user_tags admin administrator
rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"

# 自定义配置文件
# 在rabbitmq 3.7.0 之前，rabbitmq.conf 使用了Erlang语法配置格式，新的版本使用了sysctl 格式.
cd /etc/rabbitmq
touch rabbitmq.conf
```

```shell
# 访问图形界面
http://Your_Server_IP:15672/

# api文档
http://Your_Server_IP:15672/api
```

## 常见错误处理
    
### 文件大小限制
<font color="#d44375">Recovering 69477 queues, available file handles: 924. Please increase max open file handles limit to at least 69477!</font>

```shell
# 打开文件大小限制
# https://stackoverflow.com/questions/46240032/rabbitmq-file-descriptor-limit
$ sudo systemctl edit rabbitmq-server.service # 找到systemd service 地址
$ sudo vim /etc/systemd/system/rabbitmq-server.service.d/limits.conf

[Service]
LimitNOFILE=300000
```

### Mnesia is overloaded
<font color="#d44375">Mnesia is overloaded: {dump_log,write_threshold}</font>

```shell
# 编辑配置文件
$ sudo vim /etc/rabbitmq/rabbitmq.config
[
    {mnesia, [{dump_log_write_threshold, 5000}]},
    {rabbit, [{tcp_listeners, [5673]}]}
].
```