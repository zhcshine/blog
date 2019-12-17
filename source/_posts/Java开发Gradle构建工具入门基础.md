---
title: Java开发Gradle构建工具入门基础
date: 2019-08-16 10:40:45
tags:
- Java
- Gradle
categories:
- 后端
---

## Linux安装Gradle
```shell
# 从官网下载二进制包
# https://gradle.org/install/
$ unzip unzip gradle-5.6-all.zip
$ sudo mv gradle-5.6 /usr/java_jar
```

```shell
# 添加环境变量
$ sudo vim /etc/profile
```
```shell
# 最后添加
export PATH=$PATH:/usr/java_jar/gradle-5.6/bin
```
```shell
# 验证
$ gradle -v
Welcome to Gradle 5.6!

Here are the highlights of this release:
 - Incremental Groovy compilation
 - Groovy compile avoidance
 - Test fixtures for Java projects
 - Manage plugin versions via settings script

For more details see https://docs.gradle.org/5.6/release-notes.html


------------------------------------------------------------
Gradle 5.6
------------------------------------------------------------

Build time:   2019-08-14 21:05:25 UTC
Revision:     f0b9d60906c7b8c42cd6c61a39ae7b74767bb012

Kotlin:       1.3.41
Groovy:       2.5.4
Ant:          Apache Ant(TM) version 1.9.14 compiled on March 12 2019
JVM:          11.0.4 (Oracle Corporation 11.0.4+10-LTS)
OS:           Linux 4.19.49-1-MANJARO amd64

```

## 配置Gradle

### 项目配置国内源

```shell
# 配置国内aliyun源
repositories {
    maven {url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    mavenCentral()
}
```

### 全局配置国内源
```shell
vim ~/.gradle/init.gradle
```

```shell
allprojects{
    repositories {
        def REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public/'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                    remove repo
                }
            }
        }
        maven {
            url REPOSITORY_URL
        }
    }
}
```