---
layout: post
title: Spring Boot 集成 Java-Mail 实现邮件发送服务
category: [Spring Boot]
tags: [Spring Boot]
---

发送邮件是我们开发中常用的功能，比如登陆、注册验证码、忘记密码还有邮箱激活等等。

今天我们使用 Spring Boot 的 `spring-boot-mail-starter` 来简化邮件发送。

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

这里的 password 不是指邮箱密码而是授权码，获取 QQ 邮箱的授权码请参考 <br/> 
<http://service.mail.qq.com/cgi-bin/help?subtype=1&&id=28&&no=1001256>

至此邮件相关的配置已完成，接下来编写发送邮件的代码。

### 简单邮件发送

发送一封只有标题和内容的简单邮件

```java
@Service("mailService")
public class MailServiceImpl implements MailService {

    private static final Logger log = LoggerFactory.getLogger(MailServiceImpl.class);
    
    @Autowired
    private JavaMailSender javaMailSender;

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

Mail 类如下

```java
public class Mail {

    private String username;
    private String to;
    private String subject;
    private String text;
    private File file;
    private Date sendDate;
    public Mail(){}
    ......
}
```

测试代码

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringBootMailApplicationTests {

    @Resource
    private MailService mailService;

	@Test
	public void sendSimpleMailTest() {
        Mail mail = new Mail();
        mail.setTo("211887977@qq.com");
        mail.setSubject("简单邮件");
        mail.setText("这是一封简单邮件");
        mail.setSendDate(new Date());
        mailService.sendSimpleMail(mail);
	}
}
```

### Html邮件发送

发送一封 html 邮件

```java
@Override
public Mail sendHtmlMail(Mail mail) {
    try {
        MimeMessage message = javaMailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(message, true);
        helper.setFrom(from);
        helper.setTo(mail.getTo());
        helper.setSubject(mail.getSubject());
        helper.setText(mail.getText(), true);

        javaMailSender.send(message);
        log.info("html 邮件已发送！");
    } catch (MessagingException e) {
        mail.setSuccess(false);
        log.error("html 邮件发送时发生异常！", e);
    }
    return mail;
}
```

测试代码

```java
@Test
public void sendHtmlMailTest() {
    Mail mail = new Mail();
    mail.setTo("211887977@qq.com");
    mail.setSubject("html邮件");
    mail.setText("<!DOCTYPE html>\n" +
            "<html lang=\"en\">\n" +
            "<head>\n" +
            "    <meta charset=\"UTF-8\">\n" +
            "    <title>html邮件</title>\n" +
            "</head>\n" +
            "<body>\n" +
            "<h2>这是一封html邮件</h2>\n" +
            "</body>\n" +
            "</html>");
    mail.setSendDate(new Date());
    mailService.sendHtmlMail(mail);
}
```

### 附件邮件发送

发送一封带有附件的邮件

```java
@Override
public Mail sendAttachMail(Mail mail) {
    try {
        MimeMessage message = javaMailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(message, true);
        helper.setFrom(from);
        helper.setTo(mail.getTo());
        helper.setSubject(mail.getSubject());
        helper.setText(mail.getText(), true);
        helper.addAttachment(mail.getFile().getName(), mail.getFile());

        javaMailSender.send(message);
        log.info("带附件的邮件已发送！");
    } catch (MessagingException e) {
        mail.setSuccess(false);
        log.error("带附件的邮件发送时发生异常！", e);
    }
    return mail;
}
```

测试代码

```java
@Test
public void sendAttachMailTest() {
    Mail mail = new Mail();
    mail.setTo("211887977@qq.com");
    mail.setSubject("带附件邮件");
    mail.setText("这是一封带附件的邮件");
    mail.setFile(new File("D:/a.txt"));
    mail.setSendDate(new Date());
    mailService.sendAttachMail(mail);
}
```

### Html模板邮件发送

我们在某一个网站注册账号时可能会收到这样的邮件

```text
XXX,您好，请点击下面的链接完成注册！

```

这时候需要使用模本文件并替换模板里的变量来完成，以 `thymeleaf` 模板为例

#### 在 pom.xm 文件里添加 thymeleaf 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

#### 编写 thymeleaf 模板

在 resources/templates 目录新建 `mailTemplate.xml` 文件

```html
<!DOCTYPE html>
<html lang="zh" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8"/>
    <title>html模板邮件</title>
</head>
<body>
<span th:text="${user}">xxx</span>,您好，请点击下面的链接完成注册！<br/>
<a href="#" th:href="@{https://renguangli.com/{id}(id=${id})}">激活账号</a>
</body>
</html>
```

测试代码

```java
@Autowired
private TemplateEngine templateEngine;

@Test
public void sendHtmlTemplateMailTest() {
    Mail mail = new Mail();
    mail.setTo("211887977@qq.com");
    mail.setSubject("html模板邮件");

    //创建邮件正文
    Context context = new Context();
    context.setVariable("user", "dali");
    context.setVariable("id", "666");
    String emailContent = templateEngine.process("mailTemplate", context);
    mail.setText(emailContent);
    mail.setSendDate(new Date());
    mailService.sendHtmlMail(mail);
}
```
## 效果展示

有关邮件发送我写了页面，包含发送邮件和记录邮件日志的功能，效果如下图

发送邮件

![mail-send](https://renguangli.com/images/spring-boot/spring-boot-mail-send.jpg)

邮件日志

![mail-log](https://renguangli.com/images/spring-boot/spring-boot-mail-log.jpg)

## 参考资料

<https://docs.spring.io/spring-boot/docs/1.5.16.RELEASE/reference/htmlsingle/#boot-features-email>

<https://blog.csdn.net/Tracycater/article/details/73441010?utm_source=blogxgwz0>

<http://www.ityouknow.com/springboot/2017/05/06/springboot-mail.html>



本文所有代码放在 [Github](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-mail) 上  

