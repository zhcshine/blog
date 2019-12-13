---
title: java入门基础
url: 499.html
id: 499
comments: false
categories:
  - 前端
date: 2018-01-31 09:44:05
tags:
---

# linux安装java开发环境

## 安装java


下载源码

```shell
# 从官方网站下载javaLTS版本源码并复制到当前目录
# 下载地址: https://www.oracle.com/technetwork/java/javase/downloads/index.html
$ sudo mkdir /usr/java
$ sudo cd /usr/java
$ sudo cp -rf ~/Download/jdk-11.0.3_linux-x64_bin.tar.gz .
$ sudo tar -zxvf jdk-11.0.3_linux-x64_bin.tar.gz && rm  jdk-11.0.3_linux-x64_bin.tar.gz
```

设置环境变量

```shell
$ sudo vi /etc/profile
```

```shell
#set java environment
JAVA_HOME=/usr/java/jdk-11.0.3
JRE_HOME=/usr/java/jdk-11.0.3/jre  # 如果安装包中没有jre文件夹，则改成和JAVA_HOME一致
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```

验证

```shell
$ which java
/usr/java/jdk-11.0.3/bin/java

$ java -version
java version "11.0.3" 2019-04-16 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.3+12-LTS)                                           
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.3+12-LTS, mixed mode)
```

## linux安装tomcat
```shell
$ cd /home/zhc/java
# https://tomcat.apache.org/download-90.cgi
$ wget http://mirror.bit.edu.cn/apache/tomcat/tomcat-9/v9.0.22/bin/apache-tomcat-9.0.22.tar.gz
$ tar -xzvf apache-tomcat-9.0.22.tar.gz
$ mv apache-tomcat-9.0.22 tomcat 
```
```shell
# 启动 http://127.0.0.1:8080
$ sudo ./tomcat/bin/startup.sh
Using CATALINA_BASE:   /home/zhc/work/java/tomcat
Using CATALINA_HOME:   /home/zhc/work/java/tomcat
Using CATALINA_TMPDIR: /home/zhc/work/java/tomcat/temp
Using JRE_HOME:        /usr/java/jdk-11.0.4
Using CLASSPATH:       /home/zhc/work/java/tomcat/bin/bootstrap.jar:/home/zhc/work/java/tomcat/bin/tomcat-juli.jar
Tomcat started.
```
```shell
# 关闭
$ sudo ./tomcat-9/bin/shutdown.sh
```
[centos设置为系统启动](https://www.vultr.com/docs/how-to-install-apache-tomcat-8-on-centos-7)

### idea配置tomcat服务
* 修改虚拟路径
```shell
# 以下两个配置需要保持一致
RUN/DEBUG Configurations -> Server -> URL http://localhost:8080/{ProjectName}_war_exploded/ 
RUN/DEBUG Configurations -> Deployment -> Application content /{ProjectName}_war_exploded
```

## 安装maven

{% post_link Java开发maven构建工具入门基础 %}

## 安装gradle

{% post_link Java开发gradle构建工具入门基础 %}

## 安装commons-logging

```shell
# 从官网下载二进制文件包
# http://commons.apache.org/proper/commons-logging/download_logging.cgi
$ wget http://mirrors.tuna.tsinghua.edu.cn/apache//commons/logging/binaries/commons-logging-1.2-bin.tar.gz
$ tar -xzvf commons-logging-1.2-bin.tar.gz
$ sudo mv commons-logging-1.2 /usr/java_jar/
```

##  使用sdkman管理sdk

[sdkman](https://sdkman.io/install)

# 打包

## 创建jar包

```shell
# 编写应用程序
$ sudo vim com/zhuohc/jar/HelloWorld.java
```

```java
package com.zhuohc.jar;

public class HelloWorld {

    public static void main(String[] args) {
        System.out.println("hello world!");
    }
}

```

```shell
$ javac com/zhuohc/jar/HelloWorld.java  # 编译
$ jar cvf hello.jar com/zhuohc/jar/HelloWorld.class  # 打包 参数c: 创建， 参数v: 输出具体文件信息， 参数f: 指定输出文件名
已添加清单
正在添加: com/zhuohc/jar/HelloWorld.class(输入 = 441) (输出 = 302)(压缩了 31%)
```

## 查看jar包信息

```shell
$ jar tvf hello.jar  # 查看 参数t： 查看
0 Fri Aug 16 14:21:06 CST 2019 META-INF/
66 Fri Aug 16 14:21:06 CST 2019 META-INF/MANIFEST.MF
441 Fri Aug 16 14:20:20 CST 2019 com/zhuohc/jar/HelloWorld.class
```

## 提取jar包信息

```shell
$ jar xvf hello.jar # 解压 参数x： 提取extract
已创建: META-INF/
已解压: META-INF/MANIFEST.MF
已解压: com/zhuohc/jar/HelloWorld.class
```

## jar包清单文件

```shell
创建清单文件
$ sudo vim Manifest.txt
```

Manifest.txt指定程序入口
警告： 文本文件必须以新行或回车符结束。如果最后一行不以新行或回车结束，则不会正确解析. 

```shell
# Manifest.txt
Main-Class: com.zhuohc.jar.HelloWorld

```

```shell
# 重新打包
$ jar cvfm hello.jar Manifest.txt com/zhuohc/jar/HelloWorld.class  # 参数m: 指定清单文件地址
```

```shell
# 执行jar文件
$ java -jar hello.jar
hello world!
```

```shell
# Manifest.txt 其他信息
Name: com/zhuohc/jar
Specification-Title: Java Utility Classes
Specification-Version: 1.2
Specification-Vendor: Example Tech, Inc.
Implementation-Title: com.zhuohc.jar
Implementation-Version: build57
Implementation-Vendor: Example Tech, Inc.
Main-Class: com.zhuohc.jar.HelloWorld

```