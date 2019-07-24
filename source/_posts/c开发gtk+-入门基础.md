---
title: c开发gtk+入门基础
date: 2019-07-24 13:58:51
tags:
---
#### gdk+介绍
> gtk+是c语言编写的用于开发图形界面程序的函数库。gtk+来源于gimp（gnu image minipulation program），gtk+在gdk（gimp drawing kit）基础上创建，并进行封装。

#### linux安装
```shell
# http://ftp.gnome.org/pub/gnome/sources/gtk+/3.24/
$ wget http://ftp.gnome.org/pub/gnome/sources/gtk+/3.24/gtk+-3.24.10.tar.xz
$ tar xvfJ gtk+-3.24.10.tar.xz
$ ./configure --prefix=/user/include/gtk3.24
$ make && sudo make install
$ sudo ldconfig
# 设置LD_LIBRARY_PATH
$ LD_LIBRARY_PATH="/opt/gtk/lib"
$ PATH="/opt/gtk/bin:$PATH"
$ export LD_LIBRARY_PATH PATH
```
#### 待续