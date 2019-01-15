---
layout: post
title: Spring Boot 快速入门示例
category: [Spring Boot]
tags: [Spring Boot]
---

越来越多的公司选择使用 Spring Boot 来开发系统，它到底有什么优点？为什么越来越受欢迎？

## Spring Boot 简介

Spring Boot 是由 Pivotal 团队提供的全新框架，其设计目的是用来简化新 Spring 应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。使用 Spring Boot，可以轻松的创建独立运行的程序，构建独立的服务组件，是实现分布式架构、微服务架构利器。Spring Boot 简化了第三方包的引用，通过提供的`starter`，简化了依赖包的配置。

## 快速开始

Spring Boot 项目依赖 Maven 3.2+ ，如果你还没有安装 Maven，可以按照[Maven 官方文档](https://maven.apache.org)进行安装配置。

Spring Boot 依赖项使用 `org.springframework.boot` groupId。通常，POM文件将继承 `spring-boot-starter-parent` 项目并声明对一个或多个 `starters` 的依赖关系。 Spring Boot 还提供了一个可选的 Maven 插件来创建可执行 jar。    

下面是一个典型的 Spring Boot 项目 POM 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.16.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
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

### 快速搭建 Spring Boot 项目

1、访问<http://start.spring.io/> 页面如下图所示

![image](/images/spring-boot/spring-boot-fast-build.jpg)

2、构建工具选择 Maven Project，语言选择Java，Spring Boot 最新版本为 2.0.5，这里选择 1.5.16，填写 Group 和 Artifact，在 Dependencies 处填写 web 按回车引入 web 模块，点击 `Generate Project` 下载 Spring Boot 项目压缩包。


3、解压后，导入 idea，File -> Open -> 选择解压后的文件夹 -> OK，jar 下载完成后，一个 Spring Boot 项目就搭建成功了。

### Spring Boot 项目结构

如下图所示

![image](/images/spring-boot/spring-boot-project-structure.jpg)

- `src/main/java` 存放 Java 文件
- `src/main/resources` 存放 Spring Boot 配置文件
- `src/test/java` 存放单元测试代码

### 运行 Spring Boot 项目

Spring Boot 内嵌 Tomcat，执行 DemoApplication 类主方法即可运行，当看到控制台日志以下内容说明启动成功了。

``` java
Tomcat started on port(s): 8080 (http) with context path ''
```

编写一个 Hello Spring Boot 的 Controller

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DemoController {

    @GetMapping("/")
    public String helloSpringBoot() {
        return "Hello Spring Boot";
    }
}

```

RestController 相当于 Controller + ResponseBody，该类的所有方法都会返回 json，不会跳转页面。

重新启动 Spring Boot ，打开浏览器访问 <http://localhost:8080> 页面就会显示 Hello Spring Boot

### 单元测试

打开的 `src/test/java` DemoApplicationTests.java 测试类，编写简单的http请求来测试

```java
package com.example.demo;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultHandlers;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@SpringBootTest
public class DemoApplicationTests {

    @Test
    public void contextLoads() throws Exception {
        MockMvc mockMvc = MockMvcBuilders.standaloneSetup(new DemoController()).build();

        MvcResult mvcResult = mockMvc.perform(MockMvcRequestBuilders.get("/")
                .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andReturn();

        MockMvcResultHandlers.print().handle(mvcResult);

        String response = mvcResult.getResponse().getContentAsString();
        System.err.println(response);
    }

}
```

### 热部署

只需要引入 `spring-boot-devtools` 模块即可实现热部署

```xml
 <dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

本文所有代码放在 Github 上，地址：[demo](https://github.com/renguangli/spring-boot-samples/tree/master/demo)

## 参考资料

[https://docs.spring.io/spring-boot/docs/1.5.16.RELEASE/reference/htmlsingle/](https://docs.spring.io/spring-boot/docs/1.5.16.RELEASE/reference/htmlsingle/#getting-started-maven-installation)

