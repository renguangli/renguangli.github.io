---
layout: post
title: Spring Boot 集成 Java-Mail 实现邮件发送服务
category: [Spring Boot]
tags: [Spring Boot]
---

发送邮件是我们开发中常用的功能，比如注册、登陆验证码、忘记密码还有邮箱激活等场景。

今天我们使用 Spring Boot 的 `spring-boot-mail-starter` 来实现邮件发送。

## 快速开始

### 在 pom.xml 文件添加 Mail 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

### Mail 相关配置

在 application.properties 配置文件添加以下配置
```properties
## Email (MailProperties)
# SMTP server host. For instance `smtp.example.com`
spring.mail.host=smtp.qq.com
# SMTP server port.
spring.mail.port=25
# Login user of the SMTP server.
spring.mail.username=211887977@qq.com
# Login password of the SMTP server.
spring.mail.password=fkvkwmyokpbtcajd
# Default MimeMessage encoding.
spring.mail.default-encoding=utf-8
# Additional JavaMail session properties.
spring.mail.properties.mail.smtp.auth=true
# START TLS 。
spring.mail.properties.mail.smtp.starttls.enable=true
spring.mail.properties.mail.smtp.starttls.required=true
# false # Test that the mail server is available on startup.
spring.mail.test-connection=true
```

### 编写 MailServiceImpl

MailService 接口就不贴了

```java
@Service("mailService")
public class MailServiceImpl implements MailService {

    private static final Logger log = LoggerFactory.getLogger(MailServiceImpl.class);

    @Value("${spring.mail.username}")
    private String from;

    @Override
    public Mail sendSimpleMail(Mail mail) {
        SimpleMailMessage message = new SimpleMailMessage();
        message.setFrom(from);
        message.setSubject(mail.getSubject());
        message.setText(mail.getText());
        message.setTo(mail.getTo());
        message.setSentDate(mail.getSendDate());
        try {
            javaMailSender.send(message);
            log.info("success to send mail .");
        } catch (MailException e) {
            e.printStackTrace();
            log.info("failed to send mail .");
        }
        return mail;
    }
}
```

## 效果展示

有关邮件发送我写了页面，包含发送邮件和记录邮件日志的功能，效果如下图

发送邮件

![mail-send](https://renguangli.com/images/spring-boot/spring-boot-mail-send.jpg)

邮件日志

![mail-log](https://renguangli.com/images/spring-boot/spring-boot-mail-log.jpg)

除了发送简单邮件以后还会添加发送 HTML 格式，带附件，带静态资源的邮件，欢迎关注。

## 参考资料

本文所有代码放在 Github 上，地址：[spring-boot-mail](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-mail)

