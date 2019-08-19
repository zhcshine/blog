---
title: java开发spring入门基础
date: 2019-08-16 15:17:48
tags:
---

#### 构建项目基础
{% post_link java开发maven构建工具入门基础 %} 或者 {% post_link java开发gradle构建工具入门基础 %}

#### 构建项目

[spring项目初始化](!https://start.spring.io/)
1. Project: Gradle Project
2. Language: Java
3. Spring Boot: 2.17
4. Project Metadata 
    1. Group: com.zhuohc
    2. Artifact: demo
    3. Options:
        1. Name: (默认)
        2. Description: (默认)
        3. Package Name: (默认)
        4. Packaging: Jar/War(Web项目使用)
        5. Java: 11(根据java版本号选择)

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
repositories {
    maven {url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    mavenCentral()
}
