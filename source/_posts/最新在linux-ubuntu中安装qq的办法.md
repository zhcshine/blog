---
title: 最新在linux/ubuntu中安装qq的办法
url: 258.html
id: 258
comments: false
categories:
  - 办公
  - 运维
date: 2017-11-22 09:49:34
tags:
---

通过docker镜像文件的方式安装qq 1, 首先需要在主机上安装docker 2, 下载镜像文件 https://hub.docker.com/r/bestwu/qq/

    sudo docker pull bestwu/qq # 速度慢可以换镜像源
    

3, 编辑启动脚本

    vim qq.sh
    sudo docker run -d --name qq --device /dev/snd --net=host \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -v /home/YOURUSERNAME/TencentFiles:/TencentFiles \ # 此处数据卷的目录填写自己的目录
    -e DISPLAY=unix$DISPLAY \
    -e XMODIFIERS=@im=fcitx \
    -e QT_IM_MODULE=fcitx \
    -e GTK_IM_MODULE=fcitx \
    -e AUDIO_GID=63 \
    -e GID=1000 \
    -e UID=1000 \
    bestwu/qq:latest # 此处版本自行选择，作者提供了im (QQ) light,latest (QQLight) office (TIM)三个版本
    

4, 启动脚本

    ./qq.sh
    

5, 感谢作者提供的镜像，本镜像基于深度操作系统.