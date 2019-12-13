---
title: Linux开发环境常用软件
tags:
  - linux
  - ubuntu
url: 36.html
id: 36
comments: false
categories:
  - 办公
  - 运维
date: 2016-11-09 10:58:19
---

# 显示电脑硬件信息

    $ sudo add-apt-repository ppa:dawidd0811/neofetch
    $ sudo apt-get update
    $ sudo apt-get install neofetch
    neofetch
    

# 安装独立显卡驱动

https://blog.csdn.net/ghw15221836342/article/details/79571559

    # 驱动版本查询   https://www.nvidia.com/Download/index.aspx?lang=en-us
    # 安装
    sudo add-apt-repository ppa:graphics-drivers/ppa  
    sudo apt-get update  
    sudo apt-get install nvidia-390 #此处要根据上面查询到的版本适当更改
    sudo apt-get install mesa-common-dev  
    sudo apt-get install freeglut3-de
    # 测试
    nvidia-smi  #若出现电脑GPU列表，即安装成功
    或者
    nvidia-settings #显示你的显卡信息
    ````
    
    
    # Chrome浏览器
    下载地址 [百度云盘](http://pan.baidu.com/s/1hq0Nom4)
    
    # 配置SS
    

安装
==

apt-get install python-pip pip install s\_h\_a\_d\_o\_w\_s\_o\_c\_k\_s

启动
==

sslocal -s server\_ip -p server\_port -l 1080 -k password -t 600 -m aes-256-cfb

简写
==

sslocal -s ip -p port -k "password"

    开机启动配置  
    在/home下新建文件ss.sh脚本
    

#!/bin/bash #文件名:ss.sh sslocal -s ip -p port -k "password"

    编辑开机启动文件
    

sudo vim /etc/rc.local ```

    # 最后加入以下内容
    nohup bash /home/ss.sh>/home/ss.txt &
    

代理设置

    service： 127.0.0.1
    port ： 1080
    type： socks5
    

# 配置proxychains

    # 安装
    sudo apt install proxychains
    sudo vim /etc/proxychains.conf
    ~ socks5 127.0.0.1 1080  # 最后一行加入socks代理
    # 测试
    proxychains wget https://www.google.com
    

# wine qq（现在推荐docker安装qq）

安装wine1.7以上版本  
安装WineQQ7.8-20151109-Longene.deb版本  
下载[网盘](https://pan.baidu.com/s/1bpLn0vl)

# elinks 纯文本浏览器

    sudo apt-get install elinks   # 安装
    

    elinks www.zhuohc.com  # 使用
    

# unzip / zip

解决中文乱码

    unzip -O CP936 xxx.zip
    

解压到指定目录

    unzip -O xxx.zip -d xxx/
    

压缩文件

    zip -r yasuo.zip abc.txt dir1
    

# wps office办公

显示字体确实错误 [下载字体](https://pan.baidu.com/s/1bpLn0vl)  
将字体复制到linux字体中

    sudo cp * /usr/share/fonts
    

生成字体索引信息

    sudo mkfontscale
    
    sudo mkfontdir
    

更新字体缓存

    sudo fc-cache
    

中文无法输入

    $ vi /usr/bin/wps  # word文档
    

    #!/bin/bash
    export XMODIFIERS="@im=fcitx"  # 添加的内容  
    export QT_IM_MODULE="fcitx"     # 添加的内容    
    gOpt=
    #gOptExt=-multiply
    gTemplateExt=("wpt" "dot" "dotx")
    

    $ vi /usr/bin/et   # excel表格
    

    #!/bin/bash
    export XMODIFIERS="@im=fcitx"  # 添加的内容  
    export QT_IM_MODULE="fcitx"  # 添加的内容  
    gOpt=
    #gOptExt=-multiply
    

# 输入法

系统自带或者sougou输入法 ， 倬彼昊天系列皮肤

# 截屏

系统设置>键盘>自定义快捷键>+>  
名称填screenshot  
命令

    gnome-screenshot -a
    

在disabled修改快捷键

# 下载

安装uget

    sudo add-apt-repository ppa:plushuang-tw/uget-stable 
    sudo apt-get update 
    sudo apt-get install uget
    

安装aria2

    sudo add-apt-repository ppa:t-tujikawa/ppa 
    sudo apt-get update 
    sudo apt-get install aria2
    

在uget gui里编辑插件，选择aria2

# 美化监视工具

     sudo apt-get install conky  
     sudo add-apt-repository ppa:teejee2008/ppa
     sudo apt-get update
     sudo apt-get install conky-manager
     ```
     显示桌面会自动隐藏conky的bug解决办法:  
     编辑配置文件
     ```
     # own_window_type normal
    own_window_type desktop
    

\# 快速搜索文件

    sudo add-apt-repository ppa:synapse-core/testing
    sudo apt-get update
    sudo apt-get install synapse  
    

# 卸载synapse

    sudo apt-get remove synapse
    

# 移除PPA

    sudo apt-get install ppa-purge
    sudo ppa-purge ppa:synapse-core/testing
    

# 安装oh-my-zsh

    # https://github.com/robbyrussell/oh-my-zsh
    $ sudo apt-get install git
    $ sudo apt-get install zsh
    $ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    $ chsh -s /bin/zsh # 更改默认bash
    //或:
    $ chsh -s `which zsh`
    $ sudo reboot
    

# terminator 终端管理软件

安装

    sudo add-apt-repository ppa:gnome-terminator
    sudo apt-get update
    sudo apt-get install terminator
    

基本设置方法

> 右键点击终端任意区域，选择首选项

设置[solarized](https://github.com/ghuntley/terminator-solarized)配色（[参考](http://blog.codinglabs.org/articles/getting-started-with-ubuntu.html)）

    cd ~/.config/terminator  
    vim config
    

覆盖以下内容

    [global_config]
        title_transmit_bg_color = "#d30102"
        focus = system
        suppress_multiple_term_dialog = True
    [keybindings]
    [profiles]
        [[default]]
            palette = "#073642:#dc322f:#859900:#b58900:#268bd2:#d33682:#2aa198:#eee8d5:#002b36:#cb4b16:#586e75:#657b83:#839496:#6c71c4:#93a1a1:#fdf6e3"
            copy_on_selection = True
            background_image = None
            background_darkness = 0.95
            background_type = transparent
            use_system_font = False
            cursor_color = "#eee8d5"
            foreground_color = "#839496"
            show_titlebar = False
            font = Monospace 11
            background_color = "#002b36"
        [[solarized-dark]]
            palette = "#073642:#dc322f:#859900:#b58900:#268bd2:#d33682:#2aa198:#eee8d5:#002b36:#cb4b16:#586e75:#657b83:#839496:#6c71c4:#93a1a1:#fdf6e3"
            background_color = "#002b36"
            background_image = None
            cursor_color = "#eee8d5"
            foreground_color = "#839496"
        [[solarized-light]]
            palette = "#073642:#dc322f:#859900:#b58900:#268bd2:#d33682:#2aa198:#eee8d5:#002b36:#cb4b16:#586e75:#657b83:#839496:#6c71c4:#93a1a1:#fdf6e3"
            background_color = "#fdf6e3"
            background_image = None
            cursor_color = "#002b36"
            foreground_color = "#657b83"
    [layouts]
        [[default]]
            [[[child1]]]
                type = Terminal
                parent = window0
                profile = default
            [[[window0]]]
                type = Window
                parent = ""
    [plugins]
    

以上配置后会修改配色方案，但是没有bright色  
[dircolors-solarized](https://github.com/seebi/dircolors-solarized)项目提供了适合于solarized的dircolors配色方案，只要选择合适的方案使用就可以了。

    curl https://raw.github.com/seebi/dircolors-solarized/master/dircolors.ansi-dark > ~/.dircolors
    

然后在~/.bashrc中加入如下配置：

    # enable color support of ls and also add handy aliases
    if [ -x /usr/bin/dircolors ]; then
        test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
        alias ls='ls --color=auto'
        #alias dir='dir --color=auto'
        #alias vdir='vdir --color=auto'
    
        alias grep='grep --color=auto'
        alias fgrep='fgrep --color=auto'
        alias egrep='egrep --color=auto'
    fi
    
    # some more ls aliases
    alias ll='ls -alF'
    alias la='ls -A'
    alias l='ls -CF'
    

执行

    source ~/.bashrc
    

# tmux配合terminator使用

安装

    sudo apt-get install tmux
    

配置

    touch .tmux.conf
    

配置内容和使用方法

    # 设置前缀为Ctrl + x
    set -g prefix C-x
    # 解除Ctrl+b 与前缀的对应关系
    unbind C-b
    # 将r 设置为加载配置文件，并显示"reloaded!"信息
    bind r source-file ~/.tmux.conf \; display "Reloaded!"
    
    
    
    # copy-mode 将快捷键设置为vi 模式
    setw -g mode-keys vi
    
    # tmux-powerline设置
    set-option -g status on
    set-option -g status-interval 2
    set-option -g status-utf8 on
    set-option -g status-justify "centre"
    set-option -g status-left-length 60
    set-option -g status-right-length 90
    set-option -g status-left "#(~/.tmux/tmux-powerline/powerline.sh left)"
    set-option -g status-right "#(~/.tmux/tmux-powerline/powerline.sh right)"
    # 将窗口列表换成 powerline的风格
    set-window-option -g window-status-current-format "#[fg=colour235, bg=colour27]⮀#[fg=colour255, bg=colour27] #I ⮁ #W #[fg=colour27, bg=colour235]⮀"
    # 设置默认bash
    set-option -g default-shell /bin/zsh
    
    
    ########## 复制模式  ##########
    
    # copy mode 复制模式
    # 'prefix +  [ '进入复制模式
    # 按space 开始复制
    # Enter退出复制模式
    

需要安装powerline插件

    https://github.com/erikw/tmux-powerline
    

显示日期的星期

    vim data_day.sh
     # date +"%a"改成大写
     date +"%A"
    

powerline字体安装查看 http://powerline.readthedocs.io/en/latest/installation/linux.html 在terminator配置自动启动turm

    首选项->配置>命令>自定义命令  
     ([[ -f "$TMUX" ]] && tmux -2 -S $TMUX) || (TMUX="" tmux -2)
    

自动部署多窗口任务

    touch ~/development.start
    chmod +x development.start
    

    tmux has-session -t development
    if [ $? != 0 ]
    then
        tmux new-session -s development -n editor -d
        tmux send-keys -t development 'vim' C-m
        tmux split-window -v -t development
        tmux select-layout -t development main-horizontal
        tmux new-window -n bash -t development
        tmux new-window -n ssh -t development
        tmux new-window -n mysql -t development
        tmux new-window -n front-end -t development
        tmux select-window -t development:1
    fi
    tmux attach -t development
    

执行文件

    ~/development.start
    

# Visual Studio Code

通过官方PPA来安装Ubuntu Make

    sudo add-apt-repository ppa:ubuntu-desktop/ubuntu-make
    sudo apt-get update
    sudo apt-get install ubuntu-make
    

安装Ubuntu Make完后，接着使用下列命令安装Visual Studio Code

    umake web visual-studio-code
    

卸载命令

    umake web visual-studio-code --remove
    

# wine

卸载wineqq

    sudo dpkg -l | grep qq  # 找到安装包名称   
    sudo dpkg -r wine-qq7.8-longeneteam # 卸载
    

# curl http请求

    curl "http://www.baidu.com" # 如果这里的URL指向的是一个文件或者一幅图都可以直接下载到本地  
    curl -i "http://www.baidu.com" # 显示全部信息  
    curl -l "http://www.baidu.com" # 只显示头部信息  
    curl -v "http://www.baidu.com" # 显示get请求全过程解析    
    curl -d "param1=value1¶m2=value2" "http://www.baidu.com" # post请求  
    

# 安卓虚拟机genymotion

前提必须安装virtualBox  
下载地址: https://www.genymotion.com/download/  
需要先注册,下载后执行

    chmod +x genymotion-2.2.2_x64.bin
    ./genymotion-2.2.2_x64.bin -d /home/user
    

执行home/user/genymotion/genymotion

# 安装为知笔记

简易版本

    http://ppa.launchpad.net/wiznote-team/ppa/ubuntu/pool/main/w/wiznote/
    

编译安装

    http://www.wiz.cn/compile-client.html
    

chrome安装扩展

    https://chrome.google.com/webstore/detail/wiznote-web-clipper/jfanfpmalehkemdiiebjljddhgojhfab/related?hl=zh-CN
    

# 安装系统状态指示器

    sudo add-apt-repository ppa:indicator-multiload/stable-daily
    
    sudo apt-get update
    
    sudo apt-get install indicator-multiload
    

# 安装albert搜索框

    sudo add-apt-repository ppa:noobslab/macbuntu
    sudo apt-get update
    sudo apt-get install albert
    设置快捷键Ctrl+/ 样式NUMIX
    

# 下载特殊文件

    sudo apt-get install ktorrent
    sudo apt-get install amule