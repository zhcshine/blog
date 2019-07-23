---
title: java基础
url: 499.html
id: 499
comments: false
categories:
  - 前端
date: 2018-01-31 09:44:05
tags:
---

### linux安装java开发环境

    [root@localhost ~]# mkdir /usr/java
    [root@localhost ~]# cd /usr/java
    # 从官方网站下载javaLTS版本源码并复制到当前目录
    # 下载地址: https://www.oracle.com/technetwork/java/javase/downloads/index.html
    [root@localhost ~]# tar -zxvf jdk-11.0.3_linux-x64_bin.tar.gz && rm  jdk-11.0.3_linux-x64_bin.tar.gz
    

    # 设置环境变量
    vi /etc/profile
    #set java environment
    JAVA_HOME=/usr/java/jdk-11.0.3
    JRE_HOME=/usr/java/jdk-11.0.3/jre
    CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
    PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
    export JAVA_HOME JRE_HOME CLASS_PATH PATH
    

    # 验证
    [root@localhost ~]# which java
    /usr/java/jdk-11.0.3/bin/java
    [root@localhost ~]# java -version
    java version "11.0.3" 2019-04-16 LTS
    Java(TM) SE Runtime Environment 18.9 (build 11.0.3+12-LTS)                                           
    Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.3+12-LTS, mixed mode)