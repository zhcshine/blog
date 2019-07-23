---
title: centos7.2安装及使用docker
url: 557.html
id: 557
comments: false
categories:
  - 后端
  - 运维
date: 2018-09-08 14:52:53
tags:
---

    # https://docs.docker.com/install/linux/docker-ce/centos/#install-docker-ce
    # 卸载旧版本
    $ sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-selinux docker-engine-selinux docker-engine
    # 安装依赖包
    $ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
    # 安装源
    $ sudo yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    # 安装
    $ sudo yum install docker-ce
    # 启动
    $ sudo systemctl start docker
    $ sudo systemctl enable docker
    # 测试
    $ sudo docker run hello-world
    # 加入用户组(记得退出重新登录) https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user
    # 安装docker-compose
    $ sudo yum install epel-release
    $ sudo yum install python-pip
    $ sudo pip install docker-compose
    # 换源https://lug.ustc.edu.cn/wiki/mirrors/help/docker