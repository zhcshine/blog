---
title: minicat-manjaro-18.0.4-dev
date: 2019-07-24 14:11:44
tags: 
  - manjaro
  - linux
---

#### 添加中科大源

```shell
$ sudo vi /etc/pacman.conf
```
```shell
# 在文件末尾添加
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

#### 导入GPG Key

```shell
$ sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
```
#### 安装基本开发编译软件

```shell
sudo pacman -S --needed base-devel
```
#### 安装git

```shell
$ sudo pacman -S git
```

#### 安装chrome

```shell
$ sudo pacman -S google-chrome
```

#### 安装搜狗拼音输入法

```shell
$ sudo pacman -S fcitx-im #默认全部安装
$ sudo pacman -S fcitx-configtool
$ sudo pacman -S fcitx-sogoupinyin
```
#### 编辑输入法配置文件

```shell
$ sudo nano ~/.xprofile

export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
# 重启后输入法就可以正常使用。
```

#### 安装vim

```shell
$ sudo pacman -S vim
```
安装vim基础配置

```shell
$ curl https://raw.githubusercontent.com/wklken/vim-for-server/master/vimrc > ~/.vimrc
```

#### 安装tmux和.tmux

```shell
$ sudo pacman -S tmux
$ cd ~
$ git clone https://github.com/gpakosz/.tmux.git
$ ln -s -f .tmux/.tmux.conf
$ cp .tmux/.tmux.conf.local .
```

#### 安装zsh和oh-my-zsh

```shell
$ sudo pacman -S zsh
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

#### 安装yaourt

```shell
$ sudo pacman -S --needed base-devel git wget yajl
$ cd /tmp
$ git clone https://aur.archlinux.org/package-query.git
$ cd package-query/
$ makepkg -si && cd /tmp/
$ git clone https://aur.archlinux.org/yaourt.git
$ cd yaourt/
$ makepkg -si
```

#### 安装shadowsocks

```shell
$ git clone https://aur.archlinux.org/shadowsocks-qt5-git.git
$ cd shadowsocks-qt5-git
$ less PKGBUILD  # 查看
$ makepkg -si  # 安装
```

#### 设置ssh连接不断开

```shell
# vim ~/.ssh/config
Host *
    ServerAliveInterval 60
```

#### 安装jetbrain全家桶

[jetbrain激活参考地址](http://idea.lanyus.com/)

```shell
$ sudo vim /etc/hosts
# 添加以下两行代码
0.0.0.0 account.jetbrains.com
0.0.0.0 www.jetbrains.com
```

#### 安装python开发环境

{% post_link python入门基础 python开发环境 %}

#### 安装rabbitmq

```shell
$ sudo pacman -S rabbitmq rabbitmqadmin
$ sudo systemctl enable rabbitmq.service  # 开机启动
$ sudo systemctl start rabbitmq.service  # 启动
$ rabbitmq-plugins enable rabbitmq_management  # 开启web界面
```

#### 安装redis
```shell
$ sudo pacman -S redis
$ sudo systemctl enable redis.service  # 开机启动
$ sudo systemctl start redis.service  # 启动
```

设置密码

```shell
$ sudo vim /etc/redis.conf
redispassword password
```

#### 安装node

{% post_link node入门基础 python开发环境 %}

#### 其他软件清单

> peek 录制gif软件

> github desktop github客户端

> postman