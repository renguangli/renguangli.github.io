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

使用 MySQL 数据库作为存储。

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.46</version>
</dependency>
```
### Jpa 相关配置

application.properties
```properties
spring.thymeleaf.cache=false

spring.datasource.url=jdbc:mysql://localhost:3306/springboot
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=root

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
```

### 编写 Entity

```java
@Entity
@Table(name = "USER")
public class User {

    @Id
    @GeneratedValue
    private Long userId;

    @Column(name = "username", length = 16, unique = true)
    private String username;

    private String password;

    @JsonFormat(pattern = "yyyy-MM-dd")
    @DateTimeFormat(pattern = "yyyy-MM-dd")

    private char sex;

    private Integer age;

    public User(){}
    
    ...

}
```

### 编写 Repository

```java
public interface UserRepository extends JpaRepository<User, Long> {

    Page<User> findAll(Specification<User> spec, Pageable pageable);
   
    @Transactional
    void deleteByUserIdIn(Long[] userIds);

    @Transactional
    @Modifying
    @Query("update User set password = ?1 where username=?2")
    void updateUser(String password, String username);
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
    public Map<String, Object> listUsers(User user
            , @RequestParam(value = "page", defaultValue = "1") Integer page
            , @RequestParam(value = "limit", defaultValue = "10") Integer limit) {

        Pageable pageable = new PageRequest((page - 1) * limit, limit);
        Specification<User> specification = (root, query, cb) -> {
            List<Predicate> list = new ArrayList<>();
            String username = user.getUsername();
            if (username != null && !"".equals(username)){
                list.add(cb.like(root.get("username").as(String.class), username + "%"));
            }
            return cb.and(list.toArray(new Predicate[0]));
        };
        Page<User> userPage = userRepository.findAll(specification, pageable);

        Map<String, Object> data = new Hashtable<>();
        data.put("code", 0);
        data.put("msg", "success");
        data.put("data", userPage.getContent());
        data.put("count", userPage.getTotalElements());
        return data;
    }

    @GetMapping("/user")
    public boolean existsUser(Long userId) {
        return userRepository.exists(userId);
    }

    @PostMapping("/user")
    public boolean saveUser(User user) {
        user.setUsername(UUID.randomUUID().toString().replace("-", "").substring(0,15));
        user.setPassword(UUID.randomUUID().toString().replace("-", "").substring(0,11));
        if (new Random().nextInt(10) % 2 == 0) {
            user.setSex('男');
        } else {
            user.setSex('女');
        }
        user.setCreateTime(new Date());
        user.setAge(18);
        userRepository.save(user);
        return true;
    }

    @DeleteMapping("/user/{userId}")
    public boolean deleteUser(@PathVariable Long userId) {
        userRepository.delete(userId);
        return true;
    }

    @DeleteMapping("/users")
    public boolean deleteBatchUser(@RequestParam("userIds[]") Long[] userIds) {
        userRepository.deleteByUserIdIn(userIds);
        return true;
    }

    @PutMapping("/user")
    public boolean updateUser(User user) {
        userRepository.updateUser(user.getPassword(), user.getUsername());
        return true;
    }

}
```

## 效果图

![spring-boot-jpa](https://renguangli.com/images/spring-boot/spring-boot-jpa.png)

## 参考资料

本文所有代码放在 Github 上，地址：[spring-boot-jpa](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-jpa)

