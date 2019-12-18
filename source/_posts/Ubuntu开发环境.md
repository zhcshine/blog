---
title: Ubuntu开发环境
tags:
- Linux
- Ubuntu
categories:
- 办公
date: 2016-11-09 10:58:19
---

### 显示电脑硬件信息
```shell
$ sudo add-apt-repository ppa:dawidd0811/neofetch
$ sudo apt-get update
$ sudo apt-get install neofetch
neofetch
```

### 安装独立显卡驱动
```shell
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
```

### 安装代理
 {% post_link S_h_a_d_o_w_s_o_c_k_s服务 %}
 
### 配置proxychains
```shell
# 安装
sudo apt install proxychains
sudo vim /etc/proxychains.conf
~ socks5 127.0.0.1 1080  # 最后一行加入socks代理
# 测试
proxychains wget https://www.google.com
```

### 下载

安装uget
```shell
$ sudo add-apt-repository ppa:plushuang-tw/uget-stable 
$ sudo apt-get update 
$ sudo apt-get install uget
```
安装aria2
```shell
$ sudo add-apt-repository ppa:t-tujikawa/ppa 
$ sudo apt-get update 
$ sudo apt-get install aria2
```
在uget gui里编辑插件，选择aria2

### Wine qq（现在推荐docker安装qq）

安装wine1.7以上版本  
安装WineQQ7.8-20151109-Longene.deb版本  
下载[网盘](https://pan.baidu.com/s/1bpLn0vl)

卸载wineqq
```shell
$ sudo dpkg -l | grep qq  # 找到安装包名称   
$ sudo dpkg -r wine-qq7.8-longeneteam # 卸载
```

### Docker安装QQ

下载镜像文件 https://hub.docker.com/r/bestwu/qq/
```shell
$ sudo docker pull bestwu/qq # 速度慢可以换镜像源
```
```shell
$ vim qq.sh
```
```shell
$ sudo docker run -d --name qq --device /dev/snd --net=host \
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
```
```shell
$ ./qq.sh
```
### elinks 纯文本浏览器
```shell
$ sudo apt-get install elinks   # 安装
$ elinks www.zhuohc.com  # 使用
```

### unzip / zip
```shell
# 解决中文乱码
$ unzip -O CP936 xxx.zip
# 解压到指定目录
$ unzip -O xxx.zip -d xxx/
# 压缩文件
$ zip -r yasuo.zip abc.txt dir1
```

### wps office办公

*<font color="#d44375">显示字体错误 [下载字体](https://pan.baidu.com/s/1bpLn0vl)</font>*  
将字体复制到linux字体中
```shell
sudo cp * /usr/share/fonts
```
生成字体索引信息
```shell
$ sudo mkfontscale
$ sudo mkfontdir
```
更新字体缓存
```shell
sudo fc-cache
```
*<font color="#d44375">中文无法输入</font>*  
```shell
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
```
    
### 输入法

系统自带或者sougou输入法 ， 倬彼昊天系列皮肤

### 截屏

系统设置>键盘>自定义快捷键>+>  
名称填screenshot  
命令
```shell
gnome-screenshot -a
```
在disabled修改快捷键

### 美化监视工具
```shell
$ sudo apt-get install conky  
$ sudo add-apt-repository ppa:teejee2008/ppa
$ sudo apt-get update
$ sudo apt-get install conky-manager
 ```
 显示桌面会自动隐藏conky的bug解决办法:  
 编辑配置文件
 ```
# own_window_type normal
own_window_type desktop
```

### 快速搜索文件
```shell
$ sudo add-apt-repository ppa:synapse-core/testing
$ sudo apt-get update
$ sudo apt-get install synapse  
# 卸载synapse
$ sudo apt-get remove synapse
# 移除PPA
$ sudo apt-get install ppa-purge
$ sudo ppa-purge ppa:synapse-core/testing
```

### Oh-my-zsh
```shell
# https://github.com/robbyrussell/oh-my-zsh
$ sudo apt-get install git
$ sudo apt-get install zsh
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
$ chsh -s /bin/zsh # 更改默认bash
//或:
$ chsh -s `which zsh`
$ sudo reboot
```

### Terminator

```shell
$ sudo add-apt-repository ppa:gnome-terminator
$ sudo apt-get update
$ sudo apt-get install terminator
```

### Tmux
```shell
$ sudo apt-get install tmux
```

### Genymotion

前提必须安装virtualBox  
下载地址: https://www.genymotion.com/download/  
需要先注册,下载后执行
```shell
$ chmod +x genymotion-2.2.2_x64.bin
$ ./genymotion-2.2.2_x64.bin -d /home/user
```

### 安装系统状态指示器
```shell
$ sudo add-apt-repository ppa:indicator-multiload/stable-daily
$ sudo apt-get update
$ sudo apt-get install indicator-multiload
```

### Albert搜索框

    sudo add-apt-repository ppa:noobslab/macbuntu
    sudo apt-get update
    sudo apt-get install albert
    设置快捷键Ctrl+/ 样式NUMIX