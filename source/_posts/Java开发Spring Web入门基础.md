---
title: Java开发Spring Web入门基础
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

### Idea编辑器创建

![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/Idea%E5%88%9B%E5%BB%BASpring%E9%A1%B9%E7%9B%AE1.png
)
![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/Idea%E5%88%9B%E5%BB%BASpring%E9%A1%B9%E7%9B%AE2.png
)
![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/Idea%E5%88%9B%E5%BB%BASpring%E9%A1%B9%E7%9B%AE3.png
)

### Spring官方提供

[spring项目初始化](https://start.spring.io/)

* Project: Maven Project
* Language: Java
* Spring Boot: 2.17
* Project Metadata 
    * Group: com.zhuohc
    * Artifact: luckmoney
    * Options:
        * Name: (默认)
        * Description: (默认)
        * Package Name: (默认)
        * Packaging: Jar/War(Web项目使用)
        * Java: 11(根据java版本号选择)
* Developer Tools 选择 Spring Web
Generate the project

解压后的文件夹即是项目初始化的目录结构

```shell
$ tree
.
├── HELP.md
├── luckmoney.iml
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── zhuohc
    │   │           └── luckmoney
    │   │               └── LuckmoneyApplication.java  # 项目启动文件
    │   └── resources
    │       ├── application.properties  # 配置文件
    │       ├── static
    │       └── templates
    └── test
        └── java
            └── com
                └── zhuohc
                    └── luckmoney
                        └── LuckmoneyApplicationTests.java
```

修改国内源
``` text
repositories {
    maven {url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    mavenCentral()
}
```

## 启动项目

```java
// 新建文件 say方法
// src/main/java/com/zhuohc/luckmoney/HelloController.java

package com.zhuohc.luckmoney;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController  // 路由注解
public class HelloController {
//    @RequestMapping(value = "/hello", method = RequestMethod.GET)  // 1.x版本路由
    @GetMapping("/hello")  // 路由
    public String say() {
        return "Hello World";
    }
}

```

启动方式
```text
idea -> Run方法
```
```shell
$ mvn spring-boot:run
```
```shell
$ mvn clean package  # 打包 target目录下*.jar文件
$ java -jar target/luckmoney-0.0.1-SNAPSHOT.jar
```

```shell
# 访问
http://127.0.0.1:8080/hello
```

## 配置

properties与yml二选一
### properties配置
```python
# src/main/resources/application.properties
server.port=8081  # 端口号
server.servlet.context-path=/luckmoney  # 访问路径
```

### yml配置
```yml
# src/main/resources/application.yml
server:
  port: 8081  # 端口号
  servlet:
    context-path: /luckmoney  # 访问路径
```
```shell
# 重启并访问http://127.0.0.1:8081/luckmoney/hello
```

### 自定义配置

```yml
# 配置文件
server:
  port: 8081 # 端口号
  servlet:
    context-path: /luckmoney  # 访问路径

count: 5  # 单个配置
limit:  # 一组配置
  min: 2
  max: 9999
  desc: 最少${limit.min}元, 最多${limit.max}元
```

```java
//  单个配置
@Value("${count}")  // 配置注解
private Integer count;  // 单个配置项加载
```

```java
// 一组配置
// src/main/java/LimitConfig.java
package com.zhuohc.luckmoney;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.math.BigDecimal;

@Component
@ConfigurationProperties(prefix = "limit")  // 引入配置prefix为配置前缀
public class LimitConfig {
    private BigDecimal min;
    private BigDecimal max;
    private String desc;

    public BigDecimal getMin() {
        return min;
    }

    public void setMin(BigDecimal min) {
        this.min = min;
    }

    public BigDecimal getMax() {
        return max;
    }

    public void setMax(BigDecimal max) {
        this.max = max;
    }

    public String getDesc() {
        return desc;
    }

    public void setDesc(String desc) {
        this.desc = desc;
    }
}

```

```java
// say方法
package com.zhuohc.luckmoney;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.math.BigDecimal;

@RestController  // 路由注解
public class HelloController {
    @Autowired
    private LimitConfig limitConfig;  // 多个配置项， 用引入配置类的形式

    @Value("${count}")  // 配置注解
    private Integer count;  // 单个配置项加载

    // @RequestMapping(value = "/hello", method = RequestMethod.GET)  // 1.x版本路由
    @GetMapping("/hello")  // 路由
    public String say() {
        System.out.println(count);
        return "共"+ count + "人， " + limitConfig.getDesc();
    }
}

```

### 开发环境与生成环境
```shell
.
├── HELP.md
├── luckmoney.iml
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── zhuohc
    │   │           └── luckmoney
    │   │               ├── HelloController.java
    │   │               ├── LimitConfig.java
    │   │               └── LuckmoneyApplication.java
    │   └── resources
    │       ├── application-dev.yml  # 开发环境
    │       ├── application-prod.yml  # 生产环境
    │       ├── application.yml  # 加载环境
    │       ├── static
    │       └── templates
    └── test
        └── java
            └── com
                └── zhuohc
                    └── luckmoney
                        └── LuckmoneyApplicationTests.java

```
```yml
# src/main/resources/application.yml
spring:
  profiles:
    active: dev  # 加载环境dev/prod
```
```yml
# src/main/resources/application-dev.yml
server:
  port: 8081 # 端口号
  servlet:
    context-path: /luckmoney  # 访问路径

count: 5  # 单个配置
limit:  # 一组配置
  min: 0.01
  max: 9999
  desc: 最少${limit.min}元, 最多${limit.max}元
```
```yml
# src/main/resources/application-prod.yml
server:
  port: 8081 # 端口号
  servlet:
    context-path: /luckmoney  # 访问路径

count: 5  # 单个配置
limit:  # 一组配置
  min: 2
  max: 9999
  desc: 最少${limit.min}元, 最多${limit.max}元
```
```shell
# 使用生产环境运行
$ mvn clean package
$ java -jar -Dspring.profiles.active=prod target/luckmoney-0.0.1-SNAPSHOT.jar
```
## 控制器

## @Controller
* @Controller 处理http请求
* @RestController Spring4新加的注解， 返回json, 等同与@Controller + @ResponseBody

**使用@Controller注解需要配合模板使用**

pom.xml增加模板依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.zhuohc</groupId>
    <artifactId>luckmoney</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>luckmoney</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- 模板依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```
```html
<!--模板文件src/main/resources/templates/say.html-->
<!doctype html>
<html lang="zh-Hans">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Say</title>
</head>
<body>
hello world
</body>
</html>
```
```java
// say方法
package com.zhuohc.luckmoney;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.math.BigDecimal;

@Controller  // 路由注解
public class HelloController {
    @Autowired
    private LimitConfig limitConfig;  // 多个配置项， 用引入配置类的形式

    @Value("${count}")  // 配置注解
    private Integer count;  // 单个配置项加载

    // @RequestMapping(value = "/hello", method = RequestMethod.GET)  // 1.x版本路由
    @GetMapping("/hello")  // 路由
    public String say() {
        System.out.println(count);
//        return "共"+ count + "人， " + limitConfig.getDesc();
        return "say"; // 返回模板
    }
}
```

### @RequestMapping类注解
```java
@RestController  // 路由注解
@RequestMapping("/hello")  // 路由前缀http://127.0.0.1/luckmoney/hello1 => http://127.0.0.1/luckmoney/hello/hello1

public class HelloController {
}
```

### @GetMapping方法注解
```java
@GetMapping("/hello1")  // 单个路由
public String say1() {
    System.out.println(count);
    return "共"+ count + "人， " + limitConfig.getDesc();
}

@GetMapping({"/hello5", "/hi"})  // 数组路由
public String say5() {
    return "共"+ count + "人， " + limitConfig.getDesc();
}
```
### @PathVariable路径
```java
@GetMapping("/hello6/{id}")
public String say6(@PathVariable("id") Integer id) {
    return "id: " + id;
}
```
### @RequestParam参数
```java
@GetMapping("/hello7")
public String say7(@RequestParam("id") Integer id) {
    return "id: " + id;
}
@GetMapping("/hello8")
public String say8(@RequestParam(value = "id", required = false, defaultValue = "0") Integer id) {
    return "id: " + id;
}
```
### @PostMapping方法注解
```java
@PostMapping("/hello2") // post路由
public String say2() {
    return "共"+ count + "人， " + limitConfig.getDesc();
}
```
### @RequestMapping方法注解
```java
@RequestMapping(value = "/hello3", method = RequestMethod.GET)  // 1.x版本路由
public String say3() {
    return "共"+ count + "人， " + limitConfig.getDesc();
}

@RequestMapping("/hello4")  // 多种请求方式 post/get/put......
public String say4() {
    return "共"+ count + "人， " + limitConfig.getDesc();
}
```

### 示例
```java
---
title: Java开发Spring Web入门基础
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

### Idea编辑器创建

![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/Idea%E5%88%9B%E5%BB%BASpring%E9%A1%B9%E7%9B%AE1.png
)
![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/Idea%E5%88%9B%E5%BB%BASpring%E9%A1%B9%E7%9B%AE2.png
)
![](https://zhuohc-com.oss-cn-hangzhou.aliyuncs.com/Idea%E5%88%9B%E5%BB%BASpring%E9%A1%B9%E7%9B%AE3.png
)

### Spring官方提供

[spring项目初始化](https://start.spring.io/)

* Project: Maven Project
* Language: Java
* Spring Boot: 2.17
* Project Metadata 
    * Group: com.zhuohc
    * Artifact: luckmoney
    * Options:
        * Name: (默认)
        * Description: (默认)
        * Package Name: (默认)
        * Packaging: Jar/War(Web项目使用)
        * Java: 11(根据java版本号选择)
* Developer Tools 选择 Spring Web
Generate the project

解压后的文件夹即是项目初始化的目录结构

```shell
$ tree
.
├── HELP.md
├── luckmoney.iml
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── zhuohc
    │   │           └── luckmoney
    │   │               └── LuckmoneyApplication.java  # 项目启动文件
    │   └── resources
    │       ├── application.properties  # 配置文件
    │       ├── static
    │       └── templates
    └── test
        └── java
            └── com
                └── zhuohc
                    └── luckmoney
                        └── LuckmoneyApplicationTests.java
```

修改国内源
``` text
repositories {
    maven {url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    mavenCentral()
}
```

## 启动项目

```java
// 新建文件 say方法
// src/main/java/com/zhuohc/luckmoney/HelloController.java

package com.zhuohc.luckmoney;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController  // 路由注解
public class HelloController {
//    @RequestMapping(value = "/hello", method = RequestMethod.GET)  // 1.x版本路由
    @GetMapping("/hello")  // 路由
    public String say() {
        return "Hello World";
    }
}

```

启动方式
```text
idea -> Run方法
```
```shell
$ mvn spring-boot:run
```
```shell
$ mvn clean package  # 打包 target目录下*.jar文件
$ java -jar target/luckmoney-0.0.1-SNAPSHOT.jar
```

```shell
# 访问
http://127.0.0.1:8080/hello
```

## 配置

properties与yml二选一
### properties配置
```python
# src/main/resources/application.properties
server.port=8081  # 端口号
server.servlet.context-path=/luckmoney  # 访问路径
```

### yml配置
```yml
# src/main/resources/application.yml
server:
  port: 8081  # 端口号
  servlet:
    context-path: /luckmoney  # 访问路径
```
```shell
# 重启并访问http://127.0.0.1:8081/luckmoney/hello
```

### 自定义配置

```yml
# 配置文件
server:
  port: 8081 # 端口号
  servlet:
    context-path: /luckmoney  # 访问路径

count: 5  # 单个配置
limit:  # 一组配置
  min: 2
  max: 9999
  desc: 最少${limit.min}元, 最多${limit.max}元
```

```java
//  单个配置
@Value("${count}")  // 配置注解
private Integer count;  // 单个配置项加载
```

```java
// 一组配置
// src/main/java/LimitConfig.java
package com.zhuohc.luckmoney;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.math.BigDecimal;

@Component
@ConfigurationProperties(prefix = "limit")  // 引入配置prefix为配置前缀
public class LimitConfig {
    private BigDecimal min;
    private BigDecimal max;
    private String desc;

    public BigDecimal getMin() {
        return min;
    }

    public void setMin(BigDecimal min) {
        this.min = min;
    }

    public BigDecimal getMax() {
        return max;
    }

    public void setMax(BigDecimal max) {
        this.max = max;
    }

    public String getDesc() {
        return desc;
    }

    public void setDesc(String desc) {
        this.desc = desc;
    }
}

```

```java
// say方法
package com.zhuohc.luckmoney;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.math.BigDecimal;

@RestController  // 路由注解
public class HelloController {
    @Autowired
    private LimitConfig limitConfig;  // 多个配置项， 用引入配置类的形式

    @Value("${count}")  // 配置注解
    private Integer count;  // 单个配置项加载

    // @RequestMapping(value = "/hello", method = RequestMethod.GET)  // 1.x版本路由
    @GetMapping("/hello")  // 路由
    public String say() {
        System.out.println(count);
        return "共"+ count + "人， " + limitConfig.getDesc();
    }
}

```

### 开发环境与生成环境
```shell
.
├── HELP.md
├── luckmoney.iml
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── zhuohc
    │   │           └── luckmoney
    │   │               ├── HelloController.java
    │   │               ├── LimitConfig.java
    │   │               └── LuckmoneyApplication.java
    │   └── resources
    │       ├── application-dev.yml  # 开发环境
    │       ├── application-prod.yml  # 生产环境
    │       ├── application.yml  # 加载环境
    │       ├── static
    │       └── templates
    └── test
        └── java
            └── com
                └── zhuohc
                    └── luckmoney
                        └── LuckmoneyApplicationTests.java

```
```yml
# src/main/resources/application.yml
spring:
  profiles:
    active: dev  # 加载环境dev/prod
```
```yml
# src/main/resources/application-dev.yml
server:
  port: 8081 # 端口号
  servlet:
    context-path: /luckmoney  # 访问路径

count: 5  # 单个配置
limit:  # 一组配置
  min: 0.01
  max: 9999
  desc: 最少${limit.min}元, 最多${limit.max}元
```
```yml
# src/main/resources/application-prod.yml
server:
  port: 8081 # 端口号
  servlet:
    context-path: /luckmoney  # 访问路径

count: 5  # 单个配置
limit:  # 一组配置
  min: 2
  max: 9999
  desc: 最少${limit.min}元, 最多${limit.max}元
```
```shell
# 使用生产环境运行
$ mvn clean package
$ java -jar -Dspring.profiles.active=prod target/luckmoney-0.0.1-SNAPSHOT.jar
```
## 控制器

## @Controller
* @Controller 处理http请求
* @RestController Spring4新加的注解， 返回json, 等同与@Controller + @ResponseBody

**使用@Controller注解需要配合模板使用**

pom.xml增加模板依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.zhuohc</groupId>
    <artifactId>luckmoney</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>luckmoney</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- 模板依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```
```html
<!--模板文件src/main/resources/templates/say.html-->
<!doctype html>
<html lang="zh-Hans">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Say</title>
</head>
<body>
hello world
</body>
</html>
```
```java
// say方法
package com.zhuohc.luckmoney;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.math.BigDecimal;

@Controller  // 路由注解
public class HelloController {
    @Autowired
    private LimitConfig limitConfig;  // 多个配置项， 用引入配置类的形式

    @Value("${count}")  // 配置注解
    private Integer count;  // 单个配置项加载

    // @RequestMapping(value = "/hello", method = RequestMethod.GET)  // 1.x版本路由
    @GetMapping("/hello")  // 路由
    public String say() {
        System.out.println(count);
//        return "共"+ count + "人， " + limitConfig.getDesc();
        return "say"; // 返回模板
    }
}
```

### @RequestMapping类注解
```java
@RestController  // 路由注解
@RequestMapping("/hello")  // 路由前缀http://127.0.0.1/luckmoney/hello1 => http://127.0.0.1/luckmoney/hello/hello1

public class HelloController {
}
```

### @GetMapping方法注解
```java
@GetMapping("/hello1")  // 单个路由
public String say1() {
    System.out.println(count);
    return "共"+ count + "人， " + limitConfig.getDesc();
}

@GetMapping({"/hello5", "/hi"})  // 数组路由
public String say5() {
    return "共"+ count + "人， " + limitConfig.getDesc();
}
```
### @PathVariable路径
```java
@GetMapping("/hello6/{id}")
public String say6(@PathVariable("id") Integer id) {
    return "id: " + id;
}
```
### @RequestParam参数
```java
@GetMapping("/hello7")
public String say7(@RequestParam("id") Integer id) {
    return "id: " + id;
}
@GetMapping("/hello8")
public String say8(@RequestParam(value = "id", required = false, defaultValue = "0") Integer id) {
    return "id: " + id;
}
```
### @PostMapping方法注解
```java
@PostMapping("/hello2") // post路由
public String say2() {
    return "共"+ count + "人， " + limitConfig.getDesc();
}
```
### @RequestMapping方法注解
```java
@RequestMapping(value = "/hello3", method = RequestMethod.GET)  // 1.x版本路由
public String say3() {
    return "共"+ count + "人， " + limitConfig.getDesc();
}

@RequestMapping("/hello4")  // 多种请求方式 post/get/put......
public String say4() {
    return "共"+ count + "人， " + limitConfig.getDesc();
}
```

### 示例
```java
package com.zhuohc.luckmoney;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.math.BigDecimal;

@RestController  // 路由注解
@RequestMapping("/hello")  // 路由前缀
public class HelloController {
    @Autowired
    private LimitConfig limitConfig;  // 多个配置项， 用引入配置类的形式

    @Value("${count}")  // 配置注解
    private Integer count;  // 单个配置项加载

    @GetMapping("/hello1")  // 单个路由
    public String say1() {
        System.out.println(count);
        return "共"+ count + "人， " + limitConfig.getDesc();
    }
    
    @PostMapping("/hello2") // post路由
    public String say2() {
        return "共"+ count + "人， " + limitConfig.getDesc();
    }
    
    @RequestMapping(value = "/hello3", method = RequestMethod.GET)  // 1.x版本路由
    public String say3() {
        return "共"+ count + "人， " + limitConfig.getDesc();
    }

    @RequestMapping("/hello4")  // 多种请求方式 post/get/put......
    public String say4() {
        return "共"+ count + "人， " + limitConfig.getDesc();
    }

    @GetMapping({"/hello5", "/hi"})  // 数组路由
    public String say5() {
        return "共"+ count + "人， " + limitConfig.getDesc();
    }
    
    @GetMapping("/hello6/{id}")
    public String say6(@PathVariable("id") Integer id) {
        return "id: " + id;
    }
    
    @GetMapping("/hello7")
    public String say7(@RequestParam("id") Integer id) {
        return "id: " + id;
    }
    
    @GetMapping("/hello8")
    public String say8(@RequestParam(value = "id", required = false, defaultValue = "0") Integer id) {
        return "id: " + id;
    }
}
```

## Mysql
### 引入依赖
pom.xml
```xml
<!-- 数据库依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

### 配置数据库
```yml
# src/main/resouces/application-dev.yml
# 数据库配置
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/luckmoney
    username: root
    password: mysql
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
```

```sql
// 创建数据库
CREATE DATABASE `luckmoney` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```
 
 ```java
// src/main/java/com/zhuohc/luckmoney/Luckmoney.java
package com.zhuohc.luckmoney;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import java.math.BigDecimal;

@Entity
public class Luckmoney {
 @Id
 @GeneratedValue  // 自增注解
 private Integer id;
 private BigDecimal money;
 /**
  * 发送方
  */
 private String producer;
 /**
  * 接收方
  */
 private String consumer;
}
```
```sql
// 创建一条记录
insert into luckmoney (id, consumer, money, producer) VALUES (1, "李磊", 2.00, "寒梅梅"); 
```

```java
// src/main/java/com/zhuohc/luckmoney/LuckmoneyRepository.java
package com.zhuohc.luckmoney;

import org.springframework.data.jpa.repository.JpaRepository;

public interface LuckmoneyRepository extends JpaRepository<Luckmoney, Integer> {
}

```
```java
// src/main/java/com/zhuohc/luckmoney/LuckmoneyController.java
package com.zhuohc.luckmoney;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.math.BigDecimal;
import java.util.List;
import java.util.Optional;

@RestController
public class LuckmoneyController {

    @Autowired
    private LuckmoneyRepository repository;
    /**
     * 获取红包列表
     */
    @GetMapping("/luckmoneys")
    public List<Luckmoney> list() {
        return repository.findAll();
    }

    /**
     * 创建红包
     */
    @PostMapping("/luckmoney")
    public Luckmoney create(@RequestParam("producer") String producer,
                            @RequestParam("money")BigDecimal money) {
        Luckmoney luckmoney = new Luckmoney();
        luckmoney.setProducer(producer);
        luckmoney.setMoney(money);
        return repository.save(luckmoney);
    }
    /**
     * 查询红包
     */
    @GetMapping("/luckmoney/{id}")
    public Luckmoney get(@PathVariable("id") Integer id) {
        return repository.findById(id).orElse(null);
    }
    /**
     * 更新红包
     */
    @PutMapping("/luckmoney/{id}")
    public Luckmoney update(@PathVariable("id") Integer id,
                            @RequestParam("consumer") String consumer) {
        Optional<Luckmoney> optional = repository.findById(id);
        if (optional.isPresent()) {
            Luckmoney luckmoney = optional.get();
            luckmoney.setConsumer(consumer);
            return repository.save(luckmoney);
        }
        return null;
    }
}
```