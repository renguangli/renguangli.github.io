---
layout: post
title: Spring Boot 集成 Mybatis 增删改查示例
category: [Spring Boot]
tags: [Spring Boot]
---

Spring Boot Mybatis 增删改查示例

## Mybatis 简介

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。

## 快速开始

### 在 pom.xml 文件添加 Mybatis 依赖

使用 MySQL 数据库作为存储。

```java
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.2</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.46</version>
</dependency>
```

解决 Idea 不识别 Mybatis 的 Mapper.xml 文件问题 

```
<build>
    <resources>
        <!-- 解决不识别mybatis的Mapper.xml文件问题 -->
        <resource>
            <directory>src/main/java</directory>
            <filtering>false</filtering>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
        </resource>
    </resources>
</build>
```

### Mybatis 相关配置

application.properties

```properties
mybatis.type-aliases-package=com.renguangli.mybatis.bean
mybatis.mapper-locations=classpath:/com/renguangli/mybatis/mapper/xml/*Mapper.xml
mybatis.configuration.map-underscore-to-camel-case=true

spring.datasource.url=jdbc:mysql://localhost:3306/springboot
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=root
```

### 编写 Employee 实体类

```java
public class Employee implements Serializable {

    private Integer empNo;

    private String firstName;

    private String lastName;

    private String gender;

    @JsonFormat(pattern = "yyyy-MM-dd")
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private Date birthDate;

    @JsonFormat(pattern = "yyyy-MM-dd")
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private Date hireDate;
    ......

}
```

### 编写 EmployeeMapper 接口

```java
public interface EmployeeMapper extends BaseMapper<Employee, Integer>{

    List<Employee> listEmployees(@Param("pojo") Employee employee,
                                 @Param("page") Integer page,
                                 @Param("size") Integer size);

    Long countEmployee(@Param("pojo") Employee employee);
}
```

BaseMapper

```java
public interface BaseMapper<T, ID> {

    T get(@Param("id") ID id);

    List<T> list();

    int save(@Param("pojo") T pojo);

    int batchSave(@Param("pojos")List<T> pojos);

    int delete(@Param("id") ID id);

    int batchDelete(@Param("ids") ID[] ids);

    int update(@Param("pojo") T pojo);
}
```

### 编写 EmployeeMapper.xml 


```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.renguangli.mybatis.mapper.EmployeeMapper">

    <select id="listEmployees" resultType="Employee">
        select * from employee where 1=1
        <if test="pojo.firstName != null and pojo.firstName != ''">
            and first_name = #{pojo.firstName}
        </if>
        <if test="pojo.lastName != null and pojo.lastName != ''">
            and last_name = #{pojo.lastName}
        </if>
        limit #{page},#{size}
    </select>

    <select id="get" resultType="Employee">
        select * from employee where emp_no = #{id}
    </select>

    <select id="countEmployee" resultType="long">
        select count(*) from employee where 1=1
        <if test="pojo.firstName != null and pojo.firstName != ''">
            and first_name = #{pojo.firstName}
        </if>
        <if test="pojo.lastName != null and pojo.lastName != ''">
            and last_name = #{pojo.lastName}
        </if>
    </select>

    <insert id="save">
        insert into employee(emp_no,birth_date,first_name,last_name,gender,hire_date)
        values(#{pojo.empNo},#{pojo.birthDate},#{pojo.firstName},#{pojo.lastName},#{pojo.gender},#{pojo.hireDate})
    </insert>

    <delete id="delete">
        delete from employee where emp_no = #{id}
    </delete>

    <update id="update">
        update employee
        set last_name = #{pojo.lastName}
        where emp_no = #{pojo.empNo}
    </update>
</mapper>
```

### 编写 Controller

为减少代码量就不写 Service 了

```java
@RestController
public class EmployeeController {

    private final EmployeeMapper employeeMapper;

    @Autowired
    public EmployeeController(EmployeeMapper employeeMapper) {
        this.employeeMapper = employeeMapper;
    }

    @GetMapping("/employees")
    public Result listEmployees(Employee employee,
                                @RequestParam(value = "page", defaultValue = "1") Integer page,
                                @RequestParam(value = "size", defaultValue = "10") Integer size) {
        int offset = (page - 1) * size;
        List<Employee> employees = employeeMapper.listEmployees(employee, offset, size);
        long count = employeeMapper.countEmployee(employee);
        return new Result(employees, count, page, size);
    }

    @GetMapping("/employee/{empNo}")
    public Employee getEmployee(@PathVariable("empNo") Integer empNo) {
        Employee employee = employeeMapper.get(empNo);
        System.out.println(employee);
        return employee;
    }

    @PostMapping("/employee")
    public int saveEmployee(Employee employee) {
        employee.setBirthDate(new Date());
        employee.setHireDate(new Date());
        return employeeMapper.save(employee);
    }

    @DeleteMapping("/employee/{empNo}")
    public int deleteEmployee(@PathVariable("empNo") Integer empNo) {
        return employeeMapper.delete(empNo);
    }

    @PutMapping("/employee")
    public int updateEmployee(Employee employee) {
        return employeeMapper.update(employee);
    }

}
```

## 参考资料

本文所有代码放在 Github 上

[spring-boot-mybatis-annotation](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-mybatis-annotation)

[spring-boot-mybatis-xml](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-mybatis-xml)