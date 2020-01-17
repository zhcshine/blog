---
title: Manjaro开发环境
date: 2019-07-24 14:11:44
tags: 
- Manjaro
- Linux
categories:
- 办公
---

### 添加中科大源
```shell
$ sudo vi /etc/pacman.conf
```
```shell
# 在文件末尾添加
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

### 导入GPG Key
```shell
$ sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
```

### 安装基本开发编译软件
```shell
sudo pacman -S --needed base-devel
```

### 安装Git
```shell
$ sudo pacman -S git
```

### 安装Chrome

```shell
$ sudo pacman -S google-chrome
```

### 安装搜狗拼音输入法

```shell
$ sudo pacman -S fcitx-im #默认全部安装
$ sudo pacman -S fcitx-configtool
$ sudo pacman -S fcitx-sogoupinyin
```
### 编辑输入法配置文件

```shell
$ sudo nano ~/.xprofile

export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
# 重启后输入法就可以正常使用。
```

### 安装vim

```shell
$ sudo pacman -S vim
```

安装vim配置
```shell
# 版本1 https://github.com/wklken/vim-for-server
$ curl https://raw.githubusercontent.com/wklken/vim-for-server/master/vimrc > ~/.vimrc
```
```shell script
# 版本2 https://github.com/amix/vimrc
git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh
```

sudo使用vim配置
```shell script
vim ~/.profile  # or vim ~/.bashrc
```
```shell script
EDITOR=vim 
VISUAL=$EDITOR 
export EDITOR VISUAL 
```
```shell script
source ~/.profile
```

### 安装tmux和.tmux

```shell
$ sudo pacman -S tmux
$ cd ~
$ git clone https://github.com/gpakosz/.tmux.git
$ ln -s -f .tmux/.tmux.conf
$ cp .tmux/.tmux.conf.local .
```

### 安装zsh和oh-my-zsh

```shell
$ sudo pacman -S zsh
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### 安装yaourt

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

### 安装shadowsocks

```shell
$ git clone https://aur.archlinux.org/shadowsocks-qt5-git.git
$ cd shadowsocks-qt5-git
$ less PKGBUILD  # 查看
$ makepkg -si  # 安装
```

### 设置ssh连接不断开

```shell
# vim ~/.ssh/config
Host *
    ServerAliveInterval 60
```

### 安装Jetbrain全家桶

<font color="#d44375">请支持正版</font>

### 安装python开发环境

 {% post_link Python入门基础 python开发环境 %}

### 安装Rabbitmq

```shell
$ sudo pacman -S rabbitmq rabbitmqadmin
$ sudo systemctl enable rabbitmq.service  # 开机启动
$ sudo systemctl start rabbitmq.service  # 启动
$ rabbitmq-plugins enable rabbitmq_management  # 开启web界面
```

### 安装Redis
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

### 安装node

 {% post_link Node入门基础 python开发环境 %}


### 安装虚拟机

```shell script
#官方地址 https://wiki.manjaro.org/index.php?title=Virtualbox
# 安装virtualbox and linux*-virtualbox-host-modules
$ mhwd-kernel -li
Currently running: 5.4.0-1-MANJARO (linux54)
The following kernels are installed in your system:
   * linux54

$ sudo pacman -Syu virtualbox linux54-virtualbox-host-modules
$ sudo vboxreload  # 或者重启电脑
# 安装增强
$ pamac build virtualbox-ext-oracle
# 将当前用户添加到用户组中
$ sudo gpasswd -a $USER vboxusers
# 重启
$ sudo reboot now 
```


### 其他软件清单

* peek 录制gif软件

* github desktop github客户端

* postman

## 常见错误

> 错误：无法从 mirrors.tuna.tsinghua.edu.cn : Operation timed out after 10000 milliseconds with 0 out of 0 bytes received 获取文件 'archlinuxcn.db'
  错误：无法升级 archlinuxcn (下载数据库出错)
  错误：未能成功同步所有数据库

方案： 修改dns，增加8.8.8.8 dns解析
```shell script
$ sudoedit /etc/resolv.conf 
nameserver 10.95.44.53
nameserver 10.95.134.180
nameserver 8.8.8.8
```