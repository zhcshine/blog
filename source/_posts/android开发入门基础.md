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

#### 安装maven

{% post_link java开发maven构建工具入门基础 %}


#### 安装maven私服

[Nexus的下载地址](https://www.sonatype.com/download-oss-sonatype)

```shell
# 解压后/home/zhc/nexus
$ ./nexus/nexus-3.19.1-01/bin/nexus run  # 启动nexus
```

如果出现以下错误, 按提示安装java jdk
```markdown
No suitable Java Virtual Machine could be found on your system.
```
```shell
# 访问地址
http://127.0.0.1:8081/
```
#### 基础环境搭建

