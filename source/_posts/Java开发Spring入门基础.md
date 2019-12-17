---
title: java开发spring入门基础
date: 2019-08-16 15:17:48
tags:
- Java
- Spring
categories:
- 后端
---

## 构建项目基础
{% post_link Java开发maven构建工具入门基础 %}
{% post_link Java开发gradle构建工具入门基础 %}

## 构建项目

[spring项目初始化](https://start.spring.io/)

* Project: Gradle Project
* Language: Java
* Spring Boot: 2.17
* Project Metadata 
    * Group: com.zhuohc
    * Artifact: demo
    * Options:
        * Name: (默认)
        * Description: (默认)
        * Package Name: (默认)
        * Packaging: Jar/War(Web项目使用)
        * Java: 11(根据java版本号选择)

Generate the project

解压后的文件夹即是项目初始化的目录结构

```shell
$ tree
.
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── HELP.md
├── settings.gradle
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── zhuohc
    │   │           └── demo
    │   │               └── Application.java
    │   └── resources
    │       └── application.properties
    └── test
        └── java
            └── com
                └── zhuohc
                    └── demo
                        └── ApplicationTests.java
```

修改国内源
``` text
repositories {
    maven {url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    mavenCentral()
}
```

