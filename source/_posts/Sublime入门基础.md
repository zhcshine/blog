---
title: Sublime入门基础
tags:
- IDE
categories:
- 办公
date: 2016-11-09 15:36:07
---

## 官方网站

[http://www.sublimetext.com/3](http://www.sublimetext.com/3)

## 安装

### Ubuntu下载
```shell
$ sudo add-apt-repository ppa:webupd8team/sublime-text-3
$ sudo apt-get update
$ sudo apt-get install sublime-text-installer
```


## 插件

### Package Control

通过`Ctrl+``快捷键或者`View > Show Console`菜单打开控制台，复制粘贴如下代码回车即可。
```python
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

### 插件下载地址

[https://packagecontrol.io/](https://packagecontrol.io/)

### 注册码
> 请支持正版

## 设置

### 设置空格缩进
```json
{    
    "tab_size": 4,    
    "translate_tabs_to_spaces": true 
} 
```

### 设置行间距
```json
{
    "line_padding_bottom": 3, 
    "line_padding_top": 3,
}  
```
   
    

## 插件安装

**Emmet(懂的)**  
**ConvertToUtf8(编码转换)**  
**Sublime​Linter(代码检查工具，需要配合各语言插件包检查)**  
**SideBarEnhancements（侧边栏右键增强）**  
**Bracket​Highlighter（显示标签块？？）**  
**SublimeCodeIntel(跳转到自定义函数)**
**HTML5(html5的snippets)**  
**Alignment(自动对齐插件)**  
**SFTP(ftp上传)需要配置对应的ftp路径等信息** 
**git** 
**jquery(jquery snippets)**
**SaSS(可以支持sass)**
**DocBlockr（块状注释）**

## ubuntu安装中文输入法

**安装**
```shell
$  git clone https://github.com/lyfeyaj/sublime-text-imfix.git
```

**复制文件**
```shell
$ cd ~/sublime-text-imfix
$ sudo cp ./lib/libsublime-imfix.so /opt/sublime_text/
$ sudo cp ./src/subl /usr/bin/
```
**终端输入**
```shell
LD_PRELOAD=./libsublime-imfix.so subl
```
