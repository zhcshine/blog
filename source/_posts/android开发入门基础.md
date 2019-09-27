---
title: android开发入门基础
date: 2019-08-21 09:48:28
tags:
---

#### linux安装环境

##### 安装android-studio
```shell
# archlinux
$ git clone https://aur.archlinux.org/android-studio.git
$ makepkg -si
```

##### 安装sdk

```shell
# https://www.androiddevtools.cn/
# 下载 SDK Tools并解压
$ tar -zxvf android-sdk_r24.4.1-linux.tar
$ mv android-sdk-linux ~/android
# 下载 SDK并解压
$ mv android-5.0 ~/android/android-sdk-linux/platforms
```