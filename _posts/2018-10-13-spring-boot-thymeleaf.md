---
layout: post
title: Spring Boot 集成 Thymeleaf 模板引擎
category: [Spring Boot]
tags: [Spring Boot]
---

Thymeleaf 是适用于 Web 和独立环境的现代服务器端 Java 模板引擎。

Thymeleaf 的主要目标是为开发带来优雅的模板 - Thymeleaf 模板对于浏览器来说这些非HTML标准属性在渲染的时候会被浏览器自动忽略，因此对于前端人员来说它就是一个静态页面；而后端通过 Thymeleaf 引擎渲染的时候这些标签就会被识别，因此对于后端人员来说它又是动态页面！。

### 在 pom.xml 文件添加 Thymeleaf 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

`spring-boot-starter-thymeleaf` 中包含了 `spring-boot-starter-web` 可以将其删除。

### Thymeleaf 相关配置

Thymeleaf 的模板文件默认在 resources/templates 下，`spring.thymeleaf.cache=false` 开发时关闭缓存，要不然修改模板文件不管用，生产环境可以打开，

```properties
spring.thymeleaf.prefix=classpath:/templates
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=HTML
spring.thymeleaf.cache=false
```
以上都是默认配置，当然也可以自定义配置。

### 编写 Thymeleaf 模板文件

在 `resources/templates` 目录新建 `index.html` 文件

```html
<!DOCTYPE html>
<!-- 添加命名空间 -->
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8"/>

<!-- 1、变量表达式 ${...} -->
<!-- 使用th:text标签的值替换title标签里的值,title标签里的值只是为了给前端开发时做展示用的.这样很好的做到了前后端分离 -->
<title th:text="${title}">default title</title>
</head>
<body>

<!-- 2、消息表达式 #{...}, 也称为文本外部化、国际化或i18n -->
<span th:text="#{header.address.city}">...</span>

<div th:object="${book}">
    <span th:text="*{title}">...</span>
</div>

<!-- 链接表达式@{…},类似的标签有:th:href和th:src -->
<a th:href="@{http://www.baidu.com/}">绝对路径</a>
<a th:href="@{/}">相对路径</a>
<a th:href="@{css/bootstrap.min.css}">Content路径,默认访问resources/static下的css文件夹</a>

<!-- 字符串替换 -->
<label th:text="'Welcome to our application, ' + ${student.name} + '!'"></label>

<!-- 运算符 +,-,*,/,% -->
<label th:with="isEven=(${count} % 2 == 0)"></label>

<!-- 条件 -->
<!-- 标签只有在th:if中条件成立时才显示, th:unless相反,不成立时才显示 -->
<a th:href="@{/login}" th:if="${session.user == null}">Login</a>
<!-- 条件语句 -->
<div th:switch="${student.name}">
    <p th:case="'zhangsan'">User is an student</p>
    <p th:case="#{manager.name}">User is a manager</p>
    <!-- 默认属性default可以用*表示 -->
    <p th:case="*">User is some other thing</p>
</div>

<!-- 遍历 -->
<tr th:each="student : ${persons}">
    <td th:text="${student.id}">id</td>
    <td th:text="${student.name}">name</td>
</tr>

<!-- Utility对象,内置于Context中,可以通过#直接访问,包含#dates、#calendars、#numbers、#strings... -->
<label th:text="${#dates.createNow()}">#dates</label>
</body>
</html>
```

## 参考资料

[https://docs.spring.io/spring-boot/docs/1.5.16.RELEASE/reference/htmlsingle/](https://docs.spring.io/spring-boot/docs/1.5.16.RELEASE/reference/htmlsingle/#getting-started-maven-installation)