---
layout: post
title: Spring Boot 集成 Spring Data Jpa 增删改查示例
category: [Spring Boot]
tags: [Spring Boot]
---

使用 Spring Data Jpa 对数据库进行操作

## Jpa 与 Spring Data Jpa 的关系

JPA 是Java Persistence API 的简称，中文名 Java 持久层 API，是 JDK 5.0 注解或 XML 描述对象－关系表的映射关系，并将运行期的实体对象持久化到数据库中。

Sun 引入新的 JPA ORM 规范出于两个原因：
- 简化现有 Java EE 和 Java SE 应用开发工作；
- Sun 希望整合 ORM 技术，实现天下归一。

Spring Data Jpa 是 Spring 基于 ORM 框架、JPA 规范的基础上封装的一套 JPA 应用框架，开发者用极简的代码即可实现对数据的访问。它提供了包括增删改查等在内的常用功能，并且易于扩展！学习并使用 Spring Data JPA 可以极大提高开发效率！


## 快速开始

### 在 pom.xml 文件添加 Jpa 依赖

由于本机没有装如何数据据库，就用内嵌数据库 H2 吧。

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```
### Jpa 相关配置

application.properties
```properties
## H2 数据源
spring.datasource.url=jdbc:h2:file:../h2/jpa
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

## 是否打印 sql
spring.jpa.show-sql=true
## 自动见表类型 create/create-drop/update
spring.jpa.hibernate.ddl-auto=update
```

### 编写 Entity

```java
@Entity
@Table(name = "USER")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long userId;

    @Column(name = "username", length = 16, unique = true)
    private String username;

    private String password;

    @JsonFormat(pattern = "yyyy-MM-dd")
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private LocalDateTime createTime;

    private char sex;

    private Integer age;

    public User(){}
    
    ...

}
```

### 编写 Repository

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Transactional
    @Modifying
    @Query("update User set password = ?1 where username=?2")
    User updateUser(String password, String username);
}
```

### 编写 Controller

为减少代码量就不写 Service 了

```java
@RestController
public class UserController {

    private final UserRepository userRepository;

    @Autowired
    public UserController(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @GetMapping("/users")
    public List<User> listUsers() {
        return userRepository.findAll();
    }

    @GetMapping("/user")
    public boolean existsUser(Long userId) {
        return userRepository.exists(userId);
    }

    @PutMapping("/user")
    public Long countUser() {
        return userRepository.count();
    }

    @PostMapping("/user")
    public User saveUser(User user) {
        return userRepository.save(user);
    }

    @DeleteMapping("/user")
    public Long deleteUser(Long userId) {
        userRepository.delete(userId);
        return userId;
    }

    @PutMapping("/user")
    public User updateUser(User user) {
        return userRepository.updateUser(user.getPassword(), user.getUsername());
    }
}
```

## Spring Data Jpa 详解



## 参考资料

<http://www.ityouknow.com/springboot/2016/08/20/spring-boo-jpa.html>

本文所有代码放在 Github 上，地址：[spring-boot-jpa](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-jpa)

