---
title: ubuntu16.04安装及使用docker(一)
url: 245.html
id: 245
comments: false
categories:
  - 后端
  - 运维
date: 2017-11-20 11:45:42
tags:
---

### 删除旧版本

    sudo apt-get remove docker docker-engine docker.io
    

### 安装

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
    
    

### 将用户加入docker用户组，这样不需要输入sudo

    1. sudo cat /etc/group | grep docker 
    2. 如果不存在docker组，可以添加sudo groupadd docker 
    3. 添加当前用户到docker组，sudo usermod -aG  docker $USER 
    4. 重启docker服务,sudo systemctl restart docker 
    5. 如果权限不够，sudo chmod a+rw /var/run/docker.sock　
    6. sudo chown -R $USER ~/.docker
    

### 启动测试

    $ sudo  docker run hello-world
    Unable to find image 'hello-world:latest' locally
    
    latest: Pulling from library/hello-world
    9a0669468bf7: Pull complete
    Digest: sha256:0e06ef5e1945a718b02a8c319e15bae44f47039005530bc617a5d071190ed3fc
    Status: Downloaded newer image for hello-world:latest
    
    Hello from Docker!
    This message shows that your installation appears to be working correctly.
    
    To generate this message, Docker took the following steps:
     1. The Docker client contacted the Docker daemon.
     2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
     3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
     4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.
    
    To try something more ambitious, you can run an Ubuntu container with:
     $ docker run -it ubuntu bash
    
    Share images, automate workflows, and more with a free Docker ID:
     https://cloud.docker.com/
    
    For more examples and ideas, visit:
     https://docs.docker.com/engine/userguide/
    

### 更换镜像源

    # 可以修改 /etc/docker/daemon.json 文件并添加上 registry-mirrors 键值,没有就创建一个
    
    {
      "registry-mirrors": ["https://registry.docker-cn.com"]
    }
    

### 安装ctop查看容器状态

    # https://github.com/bcicen/ctop
    $ sudo wget https://github.com/bcicen/ctop/releases/download/v0.6.1/ctop-0.6.1-linux-amd64 -O /usr/local/bin/ctop
    $ sudo chmod +x /usr/local/bin/ctop
    

### 第一个实例

    # 第一次使用会下载debian的镜像文件
    $ sudo docker run debian echo 'hello world'
    > Unable to find image 'debian' locally
    > ......
    

    # -i -t 表示进行交互会话
    $ sudo docker run -i -t debian /user/bash
    root@622ac5689680:/#  echo 'hello world'
    > hello world
    root@622ac5689680:/# exit
    > exit
    

    # 基本命令
    $ sudo docker run -h container -i -t debian /bin/bash #-h表示主机名称
    $ sudo docker ps  # 查看当前运行的主机
    $ sudo docker ps -a # 查看全部的主机
    $sudo docker events  # 查看守护进程
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
    

### 创建dockerfile镜像

    # 创建项目
    $ mkdir cowsay
    $ cd cowsay
    $ vim Dockerfile
    ~ FROM debian:wheezy
    ~ RUN apt-get update && apt-get install -y cowsay fortune
    ~ :wq
    # 创建
    $ sudo docker build -t test/cowsay-dockerfile . 
    > Sending build content to Docker .....
    > ....
    # 运行
    $ sudo docker run test/cowsay-dockerfile /usr/games/cowsay 'Moo'
    

    # 使用entrypoint指令
    $ vim Dockerfile
    ~ FROM debian:wheezy
    ~ RUN apt-get update && apt-get install -y cowsay fortune
    ~ ENTRYPOINTER ["/usr/games/cowsay"]
    ~ :wq 
    # 创建
    $ sudo docker build -t test/cowsay-dockerfile . 
    > Sending build content to Docker .....
    > ....
    # 运行
    $ sudo docker run test/cowsay-dockerfile  'Moo'
    

    # 使用脚本解决entrypoint
    $ vim entrypoint.sh
    ~ #! /bin/bash
    ~ if [ $# -eq 0 ]; then
    ~      /usr/games/fortune | /usr/games/cowsay
    ~  else
    ~     /usr/games/cowsay "$@"
    ~   fi
    ~ :wq
    $ chmod +x entrypoint.sh
    $ vim Dockerfile
    ~ FROM debian:wheezy
    ~ RUN apt-get update && apt-get install -y cowsay fortune
    ~ COPY entrypoint.sh /
    ~ ENTRYPOINTER ["/entrypoint.sh"]
    ~ :wq 
    # 创建
    $ sudo docker build -t test/cowsay-dockerfile . 
    > Sending build content to Docker .....
    > ....
    # 运行
    $ sudo docker run test/cowsay-dockerfile 'Moo'
    

### 使用在线仓库

在线注册地址：　https://cloud.docker.com

    # 登陆
    $ sudo docker login
    $ username:
    $ password:
    # 填写作者信息
    $ vim Dockerfile
    ~ FROM debian:wheezy
    ~ MAINTAINER username <username@mail.com>
    ~ RUN apt-get update && apt-get install -y cowsay fortune
    ~ COPY entrypoint.sh /
    ~ ENTRYPOINTER ["/entrypoint.sh"]
    ~ :wq 
    # 创建
    $ sudo docker build -t username/cowsay-dockerfile . 
    > Sending build content to Docker .....
    > ....
    # 运行
    $ sudo docker run username/cowsay-dockerfile  'Moo'
    # 上传
    $ sudo docker push 
    

### 容器连接

    $ sudo docker run --name myredis -d redis # -d表示后台运行
    ~ a1696406326aaaa6c28931e886b8dd664d191b083de4aed464d584b4720d57b1
    $ docker run --rm -it --link myredis:redis redis /bin/bash
    > root@9c914447f5e1:/data# redis-cli -h redis -p 6379
    > redis:6379> ping
    > PONG
    > redis:6379> set "abc" 123
    > OK
    > redis:6379> get "abc"
    > "123"
    > redis:6379> exit
    > root@9c914447f5e1:/data# exit
    > exit
    

### 容器的备份

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
    

### 让docker容器连接互联网

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
    

### 数据卷

    #  -v指定数据卷
    $ docker run -it --name container-test -h container -v /ddata debian /bin/bash
    # 查看数据卷在本机的位置
    $ docker inspect -f { {.Mounts}} container-test  
    [{volume 4687a1d9a80dff56d951f14704a8125f1b642b50c78dac63d3a02a42ff05ffb9 /var/lib/docker/volumes/4687a1d9a80dff56d951f14704a8125f1b642b50c78dac63d3a02a42ff05ffb9/_data /ddata local  true }]
    # 可以在本机中添加文件并在容器中查看修改结果
    

    ### 在dockerfile中设置数据卷
    FROM debian:wheezy
    RUN useradd foo
    # VOLUME /ddata # 此处添加无效果，因为touch chown是在一个临时容器内的数据卷中执行的
    RUN touch /data/x
    RUN CHOWN -R foo:foo /data
    # VOLUME /ddata # 此处添加可以
    

    # docker run -v 指定到特定数据卷
    $ docker run -v /home/adrian/data:./data debian ls /data
    

    # 数据容器
    $ docker run --name dbdata postgres echo 'data-only container for postage' # 创建一个postgreSQL的容器
    # 使用--volumes-from参数，使其他容器也可以使用这个数据卷
    $ docker run -d --volumes-from dbdata --name db1 postgres
    

### 如何在开发中使用docker,以python flask为例

    # 创建项目
    $ tree identidock
    .
    ├── app
    │   └── identidock.py
    └── Dockerfile
    

    # 编辑flask文件
    $ vim app/identidock.py
    from flask import Flask
    app = Flask(__name__)
    
    @app.route('/')
    def hello_world():
        return 'hello world'
    
    if __name__ == '__main__':
        app.run(debug=True, host='0.0.0.0')
    

    # 编辑dockerfile文件
    $ vim Dockerfile
    FROM python:3.4
    
    RUN pip install Flask==0.10.1
    WORKDIR /app
    COPY app /app
    CMD ["python", "identidock.py"]
    

    # 创建镜像并使用容器
    $ docker build -t identidock .
    ...
    $ docker run -d -p 5000:5000 identidock # 后台运行并将主机的5000端口转发到docker
    $ curl localhost:5000
    ~ hello world
    
    # 将开发的代码目录挂载到docker上，这样就不必每次更改代码都需要重新bulid一次．
    $ docker run -d -p 5000:5000 -v "$PWD"/app:/app identidock  # -v "$PWD"/app:/app把位于主机的./app目录挂载到容器内，覆盖容器/app目录下的内容
    # 可以修改app目录下的文件查看改动情况
    
    # 以wsgi服务器运行flask并且修改设置用户
    $ vim Dockerfile
    FROM python:3.4
    
    RUN groupadd -r uwsgi && useradd -r -g uwsgi uwsgi # 创建用户组和用户
    RUN pip install Flask==0.10.1 uWSGI==2.0.8
    WORKDIR /app
    COPY app /app
    
    EXPOSE 9090 9191  # 声明主机和其他容器可以访问的端口号
    USER uwsgi #使用用户运行以下代码，默认docker是以root用户运行的，不安全
    CMD ["uwsgi", "--http", "0.0.0.0:9090", "--wsgi-file", "/app/identidock.py", "--callable", "app", "--stats", "0.0.0.0:9191"]
    

### 使用配置文件配置开发环境和生产环境

    # 创建脚本文件cmd.sh
    $ tree
    .
    ├── app
    │   └── identidock.py
    ├── cmd.sh
    └── Dockerfile
    
    $ vim cmd.sh
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
    

### 使用compose自动化安装创建运行docker环境

    # 安装环境
    # https://docs.docker.com/compose/install/
    $ sudo curl -L https://github.com/docker/compose/releases/download/1.17.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    $ sudo chmod +x /usr/local/bin/docker-compose
    $ docker-compose --version
    

    # 配置文件
    tree
    .
    ├── app
    │   └── identidock.py
    ├── cmd.sh
    ├── docker-compose.yml
    └── Dockerfile
    # 编辑docker-compose文件
    $ vim docker-compose.yml
    identidock:
      build: .
      ports:
        - "5000:5000"
      environment:
        ENV: DEV
      volumes:
        - ./app:/app
    ~                    
    

    # 使用方法
    $ docker-compose up # 如果有镜像则直接运行，如果没有镜像则先build
    $ docker-compose build # 重新构建镜像
    $ docker-compose ps 获取由compose管理的容器状态
    $ docker-compose logs 输出日志
    $ docker-stop 停止容器，但不会删除他们