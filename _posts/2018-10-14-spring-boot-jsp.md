---
layout: post
title: Spring Boot 集成 JSP 示例
category: [Spring Boot]
tags: [Spring Boot]
---

JSP 全名为Java Server Pages，中文名叫 Java服务器页面，其根本是一个简化的 Servlet 设计，它是由 Sun Microsystems公司倡导，许多公司参与一起建立的一种动态网页技术标准.

Spring Boot 默认不支持 JSP，如果你不喜欢 JSP，可以尝试以下 [Thymeleaf](https://renguangli.com/articles/spring-boot-thymeleaf)，它是 Spring Boot 默认支持的模板引擎。如果你习惯使用 JSP，Spring Boot 通过配置也可以支持。

## 快速开始

### 在 pom.xml 文件添加 JSP 依赖

```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <scope>provided</scope>
</dependency>
```

pom.xml 文件 packaging 改为 war，JSP不能在可执行 jar 中使用
```xml
<packaging>war</packaging>
```

### JSP 相关配置

application.properties

```properties
# jsp
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```

新建 src\main\webapp\WEB-INF\jsp 目录存放 JSP 文件

Spring boot 启动类继承 SpringBootServletInitializer 类，并重写 `configure(SpringApplicationBuilder builder)` 方法，以便 war 可以在 tomcat 下正常运行

```java
@SpringBootApplication
public class JspApplication extends SpringBootServletInitializer {

	public static void main(String[] args) {
		SpringApplication.run(JspApplication.class, args);
	}

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(JspApplication.class);
	}
}

```

### 编写 controller

```java
@Controller
public class JspController {

    @GetMapping("/user")
    public String index(HttpSession session) {
        List<User> users = new ArrayList<>();
        users.add(new User(1L, "admin", "admin", LocalDateTime.now()));
        users.add(new User(1L, "dali", "dali", LocalDateTime.now()));
        model.addAttribute("users", users);
        session.setAttribute("login_user", "admin");
    }
}
```

### 编写 JSP 文件

在 src\main\webapp\ 目录下新建 `index.jsp` 文件

```html
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
    <title>Spring Boot 集成 JSP 示例</title>
</head>
<body>
<h2>Welcome to spring-boot-jsp index page</h2>
</body>
</html>
```

在 src\main\webapp\WEB-INF\jsp 目录下新建 `user.jsp` 文件

```html
<%--
  Created by IntelliJ IDEA.
  User: rengu
  Date: 2018/10/14
  Time: 22:16
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
    <title>Spring Boot 集成 JSP 示例</title>
</head>
<body>
<h2>Welcome to spring-boot-jsp user page</h2>
<p>login user is ${login_user}</p>

<h2>用户列表</h2>
<table>
    <c:forEach var="user" items="${users}">
        <tr>
            <td>${user.userId}</td>
            <td>${user.username}</td>
            <td>${user.password}</td>
            <td>${user.createTime}</td>
        </tr>
    </c:forEach>
</table>
</body>
</html>
```

### 效果图

index.jsp

![spring-boot-jsp-index](https://renguangli.com/images/spring-boot/spring-boot-jsp-index.jpg)

user.jsp

![spring-boot-jsp-user](https://renguangli.com/images/spring-boot/spring-boot-jsp-user.jpg)


本文所有代码放在 Github 上，地址：[spring-boot-jsp](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-jsp)
