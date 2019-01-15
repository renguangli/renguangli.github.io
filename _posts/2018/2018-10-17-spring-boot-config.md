---
layout: post
title: Spring Boot 常用配置以及自定义配置
category: [Spring Boot]
tags: [Spring Boot]
---

Spring Boot 常用配置简单介绍及使用

## 多环境配置

### Spring Boot Profile

在 Spring Boot 中多环境配置文件名需要满足 application-{profile}.properties 的格式，其中 {profile} 对应环境标识
- application-dev.properties 开发环境 
- application-test.properties 测试环境 
- application-prod.properties 生产环境 

在 application.properties 文件中通过 `spring.profiles.active` 属性来设置，其值对应 {profile} 值。比如：

```properties
spring.profiles.active=dev
```

Spring Boot 在启动时会加载 `application-dev.properties` 配置文件
 
### Maven Profile

如果我们使用的构建工具是 Maven，也可以通过 Maven 的 Profile 特性来实现多环境配置以及打包。这样做的好处是我们不需要每次打包的时候改变 `spring.profiles.active` 的值，打包的时候 `mvn clean package -P ${profile}` 使用 -P 参数指定

pom.xml 配置如下：

```xml
<profiles>
    <profile>
        <id>dev</id>
        <properties>
            <profileActive>dev</profileActive>
        </properties>
        <activation>
            <!-- 开发启动 Spring Boot 时默认加载 application-dev.properties -->
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <profile>
        <id>test</id>
        <properties>
            <profileActive>test</profileActive>
        </properties>
    </profile>
    <profile>
        <id>prod</id>
        <properties>
            <profileActive>prod</profileActive>
        </properties>
    </profile>
</profiles>
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
            <excludes>
                <exclude>application-dev.properties</exclude>
                <exclude>application-test.properties</exclude>
                <exclude>application-prod.properties</exclude>
            </excludes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
            <includes>
                <include>application.properties</include>
                <include>application-${profileActive}.properties</include>
            </includes>
        </resource>
    </resources>
</build>
```

application.properties 配置文件如下

```properties
## profile (dev,test,prod) default dev
spring.profiles.active=@profileActive@
```
## 日志配置

Spring Boot 使用 Commons Logging 进行日志记录，但保留底层日志实现(Java Util Logging, Log4J2 and Logback)，默认使用 Logback 作为底层实现。

日志常用配置

```properties

## Logback 日志级别 ERROR, WARN, INFO, DEBUG or TRACE
logging.level.com.renguangli.config=debug
logging.level.org.springframework.web=info

## 日志文件名称 相对或绝对路径
logging.file=./logs/springboot.log
## 控制台日志格式
# logging.pattern.console=
## 日志文件格式
# logging.pattern.file=
```

除了在 application.properties 文件中配置日志外还可以使用 xml 配置文件格式。对于 Logback 日志框架来说 Spring Boot 默认加载文件为名为 logback-spring.xml, logback.xml 的配置文件，log4j2 默认加载 log4j2-spring.xml or log4j2.xml 的配置文件。当让也可以使用如下配置指定：

```properties
logging.config=classpath:logback.xml
```

下面是一个完整的 Logback 日志配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径     使用spring-boot的配置项LOG_PATH-->  
    <property name="LOG_HOME" value="${LOG_PATH}"/>  
    <property name="LOG_LEVEL" value="INFO" />  
    <!-- 控制台输出 -->   
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"> 
             <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符--> 
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>   
        </encoder> 
    </appender>
    <!-- 按照每天生成日志文件 -->   
    <appender name="FILE"  class="ch.qos.logback.core.rolling.RollingFileAppender">   
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件输出的文件名-->
            <FileNamePattern>${LOG_HOME}/%d{yyyy-MM-dd}-springboot.log</FileNamePattern> 
            <!--日志文件保留天数-->
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>   
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"> 
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符--> 
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>   
        </encoder> 
        <!--日志文件最大的大小-->
       <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
         <MaxFileSize>10MB</MaxFileSize>
       </triggeringPolicy>
    </appender> 
  
    <logger name="com.renguangli" level="INFO"/>
    <logger name="org.apache.ibatis" level="${LOG_LEVEL}"/>
    <logger name="org.mybatis.spring" level="${LOG_LEVEL}"/>
    <logger name="org.springframework" level="${LOG_LEVEL}"/>
    <logger name="java.sql.Connection" level="${LOG_LEVEL}"/>
    <logger name="java.sql.Statement" level="${LOG_LEVEL}"/>
    <logger name="java.sql.PreparedStatement" level="${LOG_LEVEL}"/>

    <!-- 日志输出级别 -->
    <root level="${LOG_LEVEL}">
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE" />
    </root> 
</configuration>

```

下面是一个完整的 log4j2 日志配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
	<properties>
		<property name="logPath">logs</property>
	</properties>

	<Appenders>
		<Console name="Console" target="SYSTEM_OUT">
			<PatternLayout charset="UTF-8" pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c{1}:%L - %msg%n" />
		</Console>

		<RollingFile name="RollingFile" filename="${logPath}/springboot.log" filepattern="${logPath}/%d{yyyyMMddHHmmss}-springboot.log">
			<PatternLayout charset="UTF-8" pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c{1}:%L - %msg%n" />
			<Policies>
				<SizeBasedTriggeringPolicy size="100 MB" />
			</Policies>
			<DefaultRolloverStrategy max="20" />
		</RollingFile>
		<RollingFile name="springboot" filename="${logPath}/springboot.log" filepattern="${logPath}/%d{yyyyMMddHHmmss}-springboot.log">
			<PatternLayout charset="UTF-8" pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c{1}:%L - %msg%n" />
			<Policies>
				<SizeBasedTriggeringPolicy size="100 MB" />
			</Policies>
			<DefaultRolloverStrategy max="20" />
		</RollingFile>
	</Appenders>
	
	<Loggers>
		<Root level="info">
			<AppenderRef ref="Console" />
			<AppenderRef ref="RollingFile" />
		</Root>
		<Logger name="com.renguangli" level="trace" additivity="false">
            <appender-ref ref="springboot"/>  
        </Logger> 
	</Loggers>
</Configuration>
```

## 自定义配置

### @Value

```properties
config.name=hello
config.value=world
```

对于像上面的自定义配置我们可以这样使用

```java
@Value("${config.name}")
private String name;

@Value("${config.value}")
private String value;
```

一两个配置的话还好，一旦配置多起来就比较难看，对于这种情况我们可以使用 Spring Boot 提供的 `@ConfigurationProperties` 注解来简化配置

### 配置类

```java
@Component
@ConfigurationProperties(prefix = "config")
public class ConfigProperties {

    private String name;

    private String value;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getValue() {
        return value;
    }

    public void setValue(String value) {
        this.value = value;
    }
}

```

如何使用呢？

```java
@Autowired
private ConfigProperties configProperties;
```

### 自定义配置自动提示

在 pom.xml 文件中添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

第二步我们需要配置META-INF/spring-configuration-metadata.json文件来描述。为了方便我们可以通过 idea 自动生成。

在 idea 设置中搜索 Annotation Processors，接下来勾住 Enable annonation processing 就完成了。

我们可以在编译后的文件中看到自动生成的spring-configuration-metadata.json。

![imgaes](https://renguangli.com/images/spring-boot/spring-boot-metadata.jpg)

![imgaes](https://renguangli.com/images/spring-boot/spring-boot-config.jpg)


## 参考资料

[boot-features-logging](https://docs.spring.io/spring-boot/docs/1.5.16.RELEASE/reference/htmlsingle/#boot-features-logging)

[Springboot之自定义配置](https://www.cnblogs.com/yangtianle/p/9065365.html)

[Spring Boot Profiles实现多环境下配置切换](https://blog.csdn.net/top_code/article/details/78570047?utm_source=blogxgwz0)

示例代码在 [Github](https://renguangli.com/renguangli) 上地址：[spring-boot-config](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-config)

