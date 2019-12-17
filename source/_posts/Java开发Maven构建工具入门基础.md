---
title: Java开发Maven构建工具入门基础
date: 2019-08-16 09:22:39
tags:
- Java
- Maven
categories:
- 后端
---

## 安装Maven
### Linux安装
```shell
# 从官网下载二进制包
# http://maven.apache.org/download.cgi
$ wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz
$ tar -xzvf apache-maven-3.6.1-bin.tar.gz
$ sudo mv apache-maven-3.6.1 /usr/java_jar
```

```shell
# 添加环境变量
$ sudo vim /etc/profile
```
```shell
# 最后添加
export PATH=/opt/apache-maven-3.6.1/bin:$PATH
```
```shell
# 验证
$ mvn -v
Apache Maven 3.6.1 (d66c9c0b3152b2e69ee9bac180bb8fcc8e6af555; 2019-04-05T03:00:29+08:00)
Maven home: /usr/java_jar/apache-maven-3.6.1
Java version: 11.0.4, vendor: Oracle Corporation, runtime: /usr/java/jdk-11.0.4
Default locale: zh_CN, platform encoding: UTF-8
OS name: "linux", version: "4.19.49-1-manjaro", arch: "amd64", family: "unix"
```

### 修改下载源

修改pom.xml文件repositories属性，添加国内源地址，具体见pom.xml

## pom.xml
```shell
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.imooc.maven01</groupId>
    <artifactId>maven01-model</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
    <repositories>
        <repository>
            <id>aliyun</id>
            <name>aliyun</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
        </repository>
    </repositories>
</project>
```

## 常见命令
```shell
# 创建一个项目
$ mvn archetype:generate
# 选择版本
Choose org.apache.maven.archetypes:maven-archetype-quickstart version:                                                                                                                                        
1: 1.0-alpha-1                                                                                                                                                                                                
2: 1.0-alpha-2                                                                                                                                                                                                
3: 1.0-alpha-3                                                                                                                                                                                                
4: 1.0-alpha-4                                                                                                                                                                                                
5: 1.0                                                                                                                                                                                                        
6: 1.1                                                                                                                                                                                                        
7: 1.3                                                                                                                                                                                                        
8: 1.4                                                                                                                                                                                                        
Choose a number: 8:       
Define value for property 'groupId': com.zhuohc.maven03         # groupID 项目名                                                                                                                                                        
Define value for property 'artifactId': maven03-model           # artifactId 项目名-模块名                    
Define value for property 'version' 1.0-SNAPSHOT: :             # version 版本                                                                                                                                                
Define value for property 'package' com.zhuohc.maven03: :                                                                                                                                                      
Confirm properties configuration:                                                                                                                                                                             
groupId: com.zhuohc.maven03                                                                                                                                                                                    
artifactId: maven03-model                                                                                                                                                                                           
version: 1.0-SNAPSHOT                                                                                                                                                                                         
package: com.zhuohc.maven03                                                                                                                                                                                    
```
```shell
$ mvn clean         # 删除
$ mvn compile       # 编译
$ mvn test          # 测试
$ mvn package       # 打包
$ mvn install       # 安装当前项目包到本地环境
```