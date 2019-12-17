---
title: Jetbrains使用配置
tags:
- IDE
categories:
- 办公
date: 2016-11-09 14:57:21
---

## 安装

### Ubuntu安装

下载并压缩到/opt/目录下,并在/usr/share/appliactions/下创建快捷方式的文件，命名为phpstorm.desktop,编辑如下代码，保存
```text
[Desktop Entry]
Categories=Development;
Comment[zh_CN]=
Comment=
Exec=/opt/phpstorm/bin/phpstorm.sh
GenericName[zh_CN]=IDE
GenericName=IDE
Icon=/opt/phpstorm/bin/webide.png
Name[zh_CN]=phpStorm
Name=phpStorm　　　　　　　
Path=
StartupNotify=true
Terminal=false
Type=Application
X-DBUS-ServiceName=
X-DBUS-StartupType=
X-KDE-SubstituteUID=false
X-KDE-Username=Learn Programming
```

### 输入法问题

升级到ubuntu16.04后，输入无法使用  
去安装目录bin文件夹下找到启动idea.sh,用编辑器打开，添加
```text
XMODIFIERS="@im=fcitx"
export XMODIFIERS
```
在
```text
# ---------------------------------------------------------------------
# Run the IDE.
# ---------------------------------------------------------------------
```
上面即可！

## License
> 请支持正版
    
## 界面样式

[theme](https://github.com/jkaving/intellij-colors-solarized)  
[背景修改，含theme](https://github.com/ChrisRM/material-theme-jetbrains)

### 界面配置

显示行号  
Editor->General->Appearance->Show line number  
关闭自动保存  
Appearance&Behavior->System settings->save files on frame deactivation 关闭  
Editor->General->Editor tabs->Mark modified tabs with asterisk 勾选

### theme安装

安装模板下载http://phpstorm-themes.com/  
ubuntu安装地址~/.pycharm/config/colors/ 

## 快捷键 

* *Ctrl+/* 或 *Ctrl+Shift+/* 注释（// 或者/_…_/ ）  
* *Shift+F6* 重构-重命名  
* *Ctrl+X* 删除行  
* *Ctrl+D* 复制行  
* *Ctrl+G* 查找行  
* *Ctrl+Shift+Up/Down* 代码向上/下移动。  
* *F2* 或 *Shift+F2* 高亮错误或警告快速定位  
* *Tab* 生成代码  
* 选中文本，按*Ctrl+Shift+F7*高亮显示所有该文本，按Esc高亮消失。(因为这个功能我就可以发放心的放弃sublime了)  
* *Ctrl+B*或*Ctrl+*鼠标左键单击 快速打开光标处的类或方法，（NB的功能）  
* *Ctrl+Alt+B* Go to implementation(s) 跳转方法实现处  
* *Ctrl+Shift+I* Open quick definition lookup 打开定义快速查找  
* *Alt+Up/Down* Go to previous/next method 跳转到上一个/下一个方法  
* *Ctrl+E* 最近打开的文件  
* *Alt+F1* 查找代码所在位置  
* *Ctrl+Alt+L* 格式化代码  
* *Ctrl+R* 替换文本  
* *Ctrl+F* 查找文本  
* *Ctrl+P* 方法参数提示  
* *F3* 查找下一个  
* *Shift+F3* 查找上一个  
* *Alt+Shift+F* 将当前文件加入收藏夹  
* *Ctrl+Alt+s* 打开配置窗口  
* *Ctrl+Shift+N* 通过文件名快速查找工程内的文件（必记）  
* *Ctrl+Shift+Alt+N* 通过一个字符快速查找位置（必记）  
* *Shift+Enter* 重新开始一行（无论光标在哪个位置）  
* *Ctrl+Alt+T* with…（if, else, try, catch, for, etc）用 * 来围绕选中的代码行，（ * 包括 if 、 while 、 try catch 等）  
* *Ctrl+Shift+U* Toggle case for word at caret or selected block 光标所在位置大小写  
* *Ctrl+Delete* Delete to word end 删除文字结束  
* *Ctrl+Backspace* Delete to word start 删除文字开始  
* *Ctrl+E* Recent files popup 弹出最近打开的文件  
* *F11* Toggle bookmark 切换标记，我觉得叫书签更好，就是sublime text 的F2  
* *Ctrl* + Shift + F12 Toggle maximizing editor 切换最大化编辑器  
* *Alt+Shift+F* Add to Favorites 添至收藏夹
