---
title: linux命令记录
url: 219.html
id: 219
comments: false
categories:
  - 运维
date: 2017-11-08 14:41:40
tags:
---

# 查看文件夹大小
```shell
du -h your_path --max-depth=1
```
**参数：**
> -a或-all 为每个指定文件显示磁盘使用情况，或者为目录中每个文件显示各自磁盘使用情况。 

> -b或-bytes 显示目录或文件大小时，以byte为单位。 

> -c或–total 除了显示目录或文件的大小外，同时也显示所有目录或文件的总和。 

> -D或–dereference-args 显示指定符号连接的源文件大小。 

> -h或–human-readable 以K，M，G为单位，提高信息的可读性。 

> -H或–si 与-h参数相同，但是K，M，G是以1000为换算单位,而不是以1024为换算单位。 

> -k或–kilobytes 以1024 bytes为单位。 

> -l或–count-links 重复计算硬件连接的文件。 

> -L<符号连接>或–dereference<符号连接> 显示选项中所指定符号连接的源文件大小。 

> -m或–megabytes 以1MB为单位。 

> -s或–summarize 仅显示总计，即当前目录的大小。 

> -S或–separate-dirs 显示每个目录的大小时，并不含其子目录的大小。 

> -x或–one-file-xystem 以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。 

> -X<文件>或–exclude-from=<文件> 在<文件>指定目录或文件。 

> –exclude=<目录或文件> 略过指定的目录或文件。 

> –max-depth=<目录层数> 超过指定层数的目录后，予以忽略。 

> –help 显示帮助。 

> –version 显示版本信息


# pkg-config
**作用**
> 检查库的版本号。如果所需要的库的版本不满足要求，它会打印出错误信息，避免链接错误版本的库文件。

> 获得编译预处理参数，如宏定义，头文件的位置。

> 获得链接参数，如库及依赖的其它库的位置，文件名及其它一些连接参数。

> 自动加入所依赖的其它库的设置。

**命令**
```shell
$ pkg-config --cflags --libs gdk-3.0
-I/usr/include/gtk-3.0 -I/usr/include/pango-1.0 -I/usr/include/glib-2.0 -I/usr/lib/glib-2.0/include -I/usr/lib/libffi-3.2.1/include -I/usr/include/fribidi -I/usr/include/harfbuzz -I/usr/include/freetype2 -I/usr/include/libpng16 -I/usr/include/uuid -I/usr/include/cairo -I/usr/include/pixman-1 -I/usr/include/gdk-pixbuf-2.0 -I/usr/include/libmount -I/usr/include/blkid -I/usr/include/gio-unix-2.0 -pthread -I/usr/include/libdrm -lgdk-3 -lz -lpangocairo-1.0 -lpango-1.0 -lgdk_pixbuf-2.0 -lcairo-gobject -lcairo -lgobject-2.0 -lglib-2.0 
```

**PKG_CONFIG_PATH**
环境变量PKG_CONFIG_PATH是用来设置.pc文件的搜索路径的，pkg-config按照设置路径的先后顺序进行搜索，直到找到指定的.pc 文件为止。这样，库的头文件的搜索路径的设置实际上就变成了对.pc文件搜索路径的设置。
  
在安装完一个需要使用的库后，比如Glib，一是将相应的.pc文件，如glib-2.0.pc拷贝到/usr/lib/pkgconfig目录下，二是通过设置环境变量PKG_CONFIG_PATH添加glib-2.0.pc文件的搜索路径。

```shell
# 添加环境变量PKG_CONFIG_PATH，在bash中应该进行如下设置：
$ export PKG_CONFIG_PATH=/opt/gtk/lib/pkgconfig:$PKG_CONFIG_PATH
```
```shell
# 可以执行下面的命令检查是否 /opt/gtk/lib/pkgconfig 路径已经设置在PKG_CONFIG_PATH环境变量中：
echo $PKG_CONFIG_PATH
```

**原理**
正常情况下，库文件在编译通过后会生成prefix/lib/pkgconfig目录，目录下会包含.pc文件，例如：
{% post_link C开发gtk+入门基础 安装gtk+ %}
```shell
$ ls -la /usr/include/gtk3.24/lib/pkgconfig
总用量 40
drwxr-xr-x 2 root root 4096  7月 24 15:56 .
drwxr-xr-x 4 root root 4096  7月 24 15:56 ..
-rw-r--r-- 1 root root  277  7月 24 15:56 gail-3.0.pc
-rw-r--r-- 1 root root  608  7月 24 15:56 gdk-3.0.pc
-rw-r--r-- 1 root root  608  7月 24 15:56 gdk-wayland-3.0.pc
-rw-r--r-- 1 root root  608  7月 24 15:56 gdk-x11-3.0.pc
-rw-r--r-- 1 root root  631  7月 24 15:56 gtk+-3.0.pc
-rw-r--r-- 1 root root  405  7月 24 15:56 gtk+-unix-print-3.0.pc
-rw-r--r-- 1 root root  631  7月 24 15:56 gtk+-wayland-3.0.pc
-rw-r--r-- 1 root root  631  7月 24 15:56 gtk+-x11-3.0.pc
```
```shell
$ vim gtk+-3.0

prefix=/usr/include/gtk3.24
exec_prefix=${prefix}
libdir=${exec_prefix}/lib
includedir=${prefix}/include
targets=x11 wayland
 
gtk_binary_version=3.0.0
gtk_host=x86_64-pc-linux-gnu
 
Name: GTK+
Description: GTK+ Graphical UI Library
Version: 3.24.10
Requires: gdk-3.0 atk >= 2.15.1 cairo >= 1.14.0 cairo-gobject >= 1.14.0 gdk-pixbuf-2.0 >= 2.30.0 gio-2.0 >= 2.53.4                                                                                        
Requires.private: atk atk-bridge-2.0 wayland-client >= 1.9.91 wayland-protocols >= 1.12 xkbcommon >= 0.2.0 wayland-cursor >= 1.9.91 wayland-egl  epoxy >= 1.4 fribidi >= 0.19.7 pangoft2 gio-unix-2.0 >= 2
Libs: -L${libdir} -lgtk-3
Cflags: -I${includedir}/gtk-3.0
```

# 端口与进程
```shell
$ sudo netstat -nap | grep 5555  # 查看指定端口被什么应用及进程号占用
tcp        0      0 0.0.0.0:5555            0.0.0.0:*               LISTEN      8362/python
tcp6       0      0 :::5555                 :::*                    LISTEN      8362/python

$ sudo netstat -nap | grep 8362  # 同上，查看端口号
tcp        0      0 0.0.0.0:5555            0.0.0.0:*               LISTEN      8362/python                                       │apps                 product.sh        tmp
tcp6       0      0 :::5555                 :::*                    LISTEN      8362/python                                       │➜  api git:(master) source .env/bin/activate
unix  3      [ ]         STREAM     CONNECTED     75201340 8362/python                                                            │(.env) ➜  api git:(master) pwd
unix  3      [ ]         STREAM     CONNECTED     75201341 8362/python                          

$ sudo kill -s 9 pid  # 杀死进程pid
```

# systemctl
```shell
# 可以返回systemd service文件路径
$ sudo systemctl edit rabbitmq-server.service
```