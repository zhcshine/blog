---
title: Docker入门基础
date: 2019-09-27 11:07:09
tags:
- Docker
categories:
- 运维
---

## 安装

### CentOs7.2
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
### Ubuntu

```shell
# 删除旧版本
$ sudo apt-get remove docker docker-engine docker.io
```
```shell
# 安装
$ sudo apt-get update
# 安装依赖
$ sudo apt-get install  apt-transport-https ca-certificates curl software-properties-common
# 添加官方key
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
# 查找key
$ sudo apt-key fingerprint 0EBFCD88
pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
# 获取资源
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
# 查找合适的版本
$ apt-cache madison docker-ce
docker-ce | 17.09.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
# 安装
$ sudo apt-get install docker-ce=<VERSION>
# 打印安装结果
$ sudo docker version
Client:
 Version:      17.09.0-ce
 API version:  1.32
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:42:18 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.09.0-ce
 API version:  1.32 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:40:56 2017
 OS/Arch:      linux/amd64
 Experimental: false
```

将用户加入docker用户组，这样不需要输入sudo
```vim
1. sudo cat /etc/group | grep docker 
2. 如果不存在docker组，可以添加sudo groupadd docker 
3. 添加当前用户到docker组，sudo usermod -aG  docker $USER 
4. 重启docker服务,sudo systemctl restart docker 
5. 如果权限不够，sudo chmod a+rw /var/run/docker.sock　
6. sudo chown -R $USER ~/.docker
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

```markdown
常见国内源:

Docker中国 https://registry.docker-cn.com
网易 http://hub-mirror.c.163.com
ustc  https://docker.mirrors.ustc.edu.cn
中国科技大学 https://docker.mirrors.ustc.edu.cn
阿里云 https://cr.console.aliyun.com/
```

### 安装ctop查看容器状态
```shell
# https://github.com/bcicen/ctop
$ sudo wget https://github.com/bcicen/ctop/releases/download/v0.6.1/ctop-0.6.1-linux-amd64 -O /usr/local/bin/ctop
$ sudo chmod +x /usr/local/bin/ctop
```
    
## 概念

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


## 基本命令
```shell
$ sudo docker run -h container -i -t debian /bin/bash #-h表示主机名称
$ sudo docker ps  # 查看当前运行的主机
$ sudo docker ps -a # 查看全部的主机
$ sudo docker events  # 查看守护进程
$ sudo docker inspect HOST_NAME 　# 查看HOST_NAME的信息
$ sudo docker port HOST_NAME # 查看端口映射信息
$ sudo docker diff HOST_NAME　# 查看更改的信息
$ sudo docker logs HOST_NAME   # 打印log信息
$ sudo docker rm HOST_NAME  # 删除
$ sudo docker pull redis  # 拉取镜像
$ sudo docker create redis # 创建容器
$ sudo docker start zen_brown # 开启容器（kitematic是绿色波浪线）
$ sudo docker stop zen_brown # 停止容器（kitematic是灰色直线）
$ sudo docker pause zen_brown  # 暂停容器kitematic是灰色波浪线）
$ sudo docker commit zen_brown # 将容器变成镜像
$ sudo docker run redis /bin/bash
$ sudo docker attach zen_brown # 查看容器内进程
$ sudo docker cp # 在容器和主机之间复制内容
$ sudo docker exec # 在容器中运行命令
$ sudo docker kill # 容器立即退出
```

## Dockerfile

### 编辑dockerfile文件
```shell
# 创建项目
$ mkdir cowsay
$ cd cowsay
$ vim Dockerfile
```
```text
FROM debian:wheezy
RUN apt-get update && apt-get install -y cowsay fortune
```
### 创建镜像
```shelll
# 创建
$ sudo docker build -t test/cowsay-dockerfile . 
Sending build content to Docker .....
....
# 运行
$ sudo docker run test/cowsay-dockerfile /usr/games/cowsay 'Moo'
```
### entrypoint指令
```shell
$ vim Dockerfile
```
```text
FROM debian:wheezy
RUN apt-get update && apt-get install -y cowsay fortune
ENTRYPOINTER ["/usr/games/cowsay"]
```
```shell
$ sudo docker build -t test/cowsay-dockerfile . 
Sending build content to Docker .....
....
# 运行
$ sudo docker run test/cowsay-dockerfile  'Moo'
```
### 使用脚本解决entrypoint
```shell
$ vim entrypoint.sh
```
```text
#! /bin/bash
if [ $# -eq 0 ]; then
  /usr/games/fortune | /usr/games/cowsay
else
  /usr/games/cowsay "$@"
fi
```
```shell
$ chmod +x entrypoint.sh
```
```shell
$ vim Dockerfile
```
```text
FROM debian:wheezy
RUN apt-get update && apt-get install -y cowsay fortune
COPY entrypoint.sh /
ENTRYPOINTER ["/entrypoint.sh"]
```
```shell
# 创建
$ sudo docker build -t test/cowsay-dockerfile . 
> Sending build content to Docker .....
> ....
# 运行
$ sudo docker run test/cowsay-dockerfile 'Moo'
```    

## 在线仓库

在线注册地址：　https://cloud.docker.com
```shell
# 登陆
$ sudo docker login
$ username:
$ password:
# 填写作者信息
```
```shell
$ vim Dockerfile
```
```text
FROM debian:wheezy
MAINTAINER username <username@mail.com>
RUN apt-get update && apt-get install -y cowsay fortune
COPY entrypoint.sh /
ENTRYPOINTER ["/entrypoint.sh"]
```
```shell
# 创建
$ sudo docker build -t username/cowsay-dockerfile . 
> Sending build content to Docker .....
# 运行
$ sudo docker run username/cowsay-dockerfile  'Moo'
# 上传
$ sudo docker push 
```

## 容器的备份
```shell
# 查看所有的容器
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
a1696406326a        redis               "docker-entrypoint..."   13 minutes ago      Up 13 minutes       6379/tcp            myredis
# 备份指定的id
$ docker commit -p a1696406326a redis-backup
# 查看镜像
$ docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
redis-backup                 latest              fb9d896b3a39        11 seconds ago      107MB
# 上传镜像(docker login && docker push)
# 或者打包
$ docker save -o ./redis-backup.tar redis-backup
# 恢复容器
$ docker pull ****
# 或者使用包
$ docker load -i ./redis-backup.tar
```

## docker容器连接互联网
```shell
# -p 8000:80 表示将主机的8000端口转发至容器的80端口
$ docker run -d -p 8000:80 nginx 

# 容器互联
$ docker run -d --name myredis redis
$ docker run --link myredis:redis debian env  # --link 目标容器:目标容器在主容器内的简称
~ PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
~ HOSTNAME=69df45e6f92d
~ REDIS_PORT=tcp://172.17.0.2:6379
~ REDIS_PORT_6379_TCP=tcp://172.17.0.2:6379
~ REDIS_PORT_6379_TCP_ADDR=172.17.0.2
~ REDIS_PORT_6379_TCP_PORT=6379
~ REDIS_PORT_6379_TCP_PROTO=tcp
~ REDIS_NAME=/stupefied_chandrasekhar/redis
~ REDIS_ENV_GOSU_VERSION=1.10
~ REDIS_ENV_REDIS_VERSION=4.0.2
~ REDIS_ENV_REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-4.0.2.tar.gz
~ REDIS_ENV_REDIS_DOWNLOAD_SHA=b1a0915dbc91b979d06df1977fe594c3fa9b189f1f3d38743a2948c9f7634813
~ HOME=/root
```

## 示例

### 如何在开发中使用docker,以python flask为例
```shell
# 创建项目
$ tree identidock
.
├── app
│   └── identidock.py
└── Dockerfile
```

编辑flask文件
```shell
$ vim app/identidock.py
```
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'hello world'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

编辑dockerfile文件
```shell
$ vim Dockerfile
```
```text
FROM python:3.4

RUN pip install Flask==0.10.1
WORKDIR /app
COPY app /app
CMD ["python", "identidock.py"]
```

创建镜像并使用容器
```shell
$ docker build -t identidock .
...
$ docker run -d -p 5000:5000 identidock # 后台运行并将主机的5000端口转发到docker
$ curl localhost:5000
~ hello world
```

将开发的代码目录挂载到docker上，这样就不必每次更改代码都需要重新bulid一次．
```shell
$ docker run -d -p 5000:5000 -v "$PWD"/app:/app identidock  
# -v "$PWD"/app:/app把位于主机的./app目录挂载到容器内，覆盖容器/app目录下的内容
```


以wsgi服务器运行flask并且修改设置用户
```shell
$ vim Dockerfile
```
```text
FROM python:3.4

RUN groupadd -r uwsgi && useradd -r -g uwsgi uwsgi # 创建用户组和用户
RUN pip install Flask==0.10.1 uWSGI==2.0.8
WORKDIR /app
COPY app /app

EXPOSE 9090 9191  # 声明主机和其他容器可以访问的端口号
USER uwsgi #使用用户运行以下代码，默认docker是以root用户运行的，不安全
CMD ["uwsgi", "--http", "0.0.0.0:9090", "--wsgi-file", "/app/identidock.py", "--callable", "app", "--stats", "0.0.0.0:9191"]
```
    
### 使用配置文件配置开发环境和生产环境
```shell
# 创建脚本文件cmd.sh
$ tree
.
├── app
│   └── identidock.py
├── cmd.sh
└── Dockerfile

$ vim cmd.sh
```
```shell
#! /bin/bash
set -e

if [ "$ENV" = 'DEV' ]; then
  echo "Running Development Server"
  exec python "identidock.py"
else
  echo "Running Production Server"
  exec uwsgi --http 0.0.0.0:9090 --wsgi-file /app/identidock.py --callable app --stats 0.0.0.0:9191
fi

$ chmod +x cmd.sh
$ vim Dockerfile
FROM python:3.4

RUN groupadd -r uwsgi && useradd -r -g uwsgi uwsgi
RUN pip install Flask==0.10.1 uWSGI==2.0.8
WORKDIR /app
COPY app /app
COPY cmd.sh /

EXPOSE 9090 9191
USER uwsgi
CMD ["/cmd.sh"]

$ docker build -t identidock .
$ docker run -e "ENV=DEV" -p 5000:5000 identidock # 开发环境
$ docker run -d -P identidock
```

### 使用compose自动化安装创建运行docker环境

安装环境
```shell
# https://docs.docker.com/compose/install/
$ sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
$ docker-compose --version
```

配置文件
```shell
tree
.
├── app
│   └── identidock.py
├── cmd.sh
├── docker-compose.yml
└── Dockerfile
```

编辑docker-compose文件
```shell
$ vim docker-compose.yml
```
```text
identidock:
  build: .
  ports:
    - "5000:5000"
  environment:
    ENV: DEV
  volumes:
    - ./app:/app
```
```shell
# 使用方法
$ docker-compose up # 如果有镜像则直接运行，如果没有镜像则先build
$ docker-compose build # 重新构建镜像
$ docker-compose ps 获取由compose管理的容器状态
$ docker-compose logs 输出日志
$ docker-stop 停止容器，但不会删除他们
```

