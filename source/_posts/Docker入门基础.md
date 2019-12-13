---
title: Docker入门基础
date: 2019-09-27 11:07:09
tags:
---

# 安装

### centos7.2安装
```shell
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
```


### 配置国内镜像源
```shell
$ sudo vim /etc/docker/daemon.json
```

```json
{
    "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

```shell
# 重启
$ sudo systemctl restart docker.service
```

```text
常见国内源

Docker中国 https://registry.docker-cn.com
网易 http://hub-mirror.c.163.com
ustc  https://docker.mirrors.ustc.edu.cn
中国科技大学 https://docker.mirrors.ustc.edu.cn
阿里云 https://cr.console.aliyun.com/
```

# 概念

### container 容器
```shell
# 查看全部运行的容器
$ sudo docker ps
# 删除容器
$ sudo docker rm container_id
```

### image 镜像
```shell
# 查看安装的镜像
$ sudo docker images
# 删除镜像
sudo docker rmi iamge_id
```


# Dockerfile

```shell
# Dockerfile编写
$ vim Dockerfile
```

```shell
# 制作镜像
$ sudo docker build -t zhcshine:docker-sde/tag
```

```shell
# 登录到hub.docker.com
$ sudo docker login:
username:
password:
Login Succeeded
```

```shell
# 推送到hub.docker.com
$ sudo docker push zhcshine:docker-sde
```