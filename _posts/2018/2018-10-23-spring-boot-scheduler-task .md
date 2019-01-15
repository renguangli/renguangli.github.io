---
layout: post
title: Spring Boot 中使用 @Scheduled 注解创建定时任务
category: [Spring Boot]
tags: [Spring Boot]
---

在项目日常开发过程中，经常需要定时任务来帮我们做一些工作，如清理日志。定时任务的实现方法主要有 Timer、Quartz 以及 elastic-job


## Timer 实现定时任务

### 只执行一次的定时任务

```java
Timer timer = new Timer();
timer.schedule(new TimerTask() {
    @Override
    public void run() {
        System.out.println("2000毫米后执行一次。");
    }
}, 2000);

timer.schedule(new TimerTask() {
    @Override
    public void run() {
        System.out.println("5000毫米后执行一次。");
    }
}, new Date(System.currentTimeMillis() + 5000));
```

### 循环执行任务

```java
Timer timer = new Timer();
timer.schedule(new TimerTask() {
    @Override
    public void run() {
        System.out.println(111);
    }
}, 1000, 2000); // 1000毫米后执行第一次，之后每2000毫米执行一次

```

### 终止任务

```java
timer.concel();
```


Timer 是 JDK 实现的定时任务，用起来简单、方便，对一些简单的定时任务可以使用它。由于它不支持 cron 表达式，现在已经很少用了。

## Quartz 实现定时任务

Quartz 是一个完全由　Java 编写的开源作业调度框架，可以用它来实现定时任务。

### 在 pom.xml 文件添加 Quartz 依赖

```xml
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.2.1</version>
</dependency>
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz-jobs</artifactId>
    <version>2.2.1</version>
</dependency>

<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.25</version>
</dependency>

<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-simple</artifactId>
    <version>1.7.6</version>
</dependency>
```

### 编写 Job

定时执行的任务

```java
public class QuartzJob implements Job{

    public void execute(JobExecutionContext context) throws JobExecutionException {
        JobDataMap jobDataMap = context.getJobDetail().getJobDataMap();
        String hello = (String) jobDataMap.get("hello");
        System.err.println(hello);
    }
    
}
```

### 编写 Task

```java
public void task() {
    // 该 map 可在 job 中获取
    JobDataMap map = new JobDataMap();
    map.put("hello", "world");

    JobDetail jobDetail = newJob(QuartzJob.class).
            withIdentity("myJob", "myGroup").
            setJobData(map).build();
    /*
     * 简单定时器
     *
     * 执行时间间隔
     * withIntervalInMilliSeconds 毫秒
     * withIntervalInSeconds 秒
     * withIntervalInMinutes 分钟
     * withIntervalInHours 小时
     *
     * 执行次数
     * repeatForever 重复执行
     * withRepeatCount 次数
     */
    SimpleScheduleBuilder scheduleBuilder = simpleSchedule().withIntervalInSeconds(3).withRepeatCount(10);

    /*
     * corn定时器
     *
     * corn表达式，使用更灵活
     * corn表达式在线生成 http://cron.qqe2.com/
     */
    CronScheduleBuilder  cronScheduleBuilder = CronScheduleBuilder.cronSchedule("0 0 0 1 * ?");

    Trigger trigger = newTrigger().startAt(new Date()).//startNow() 默认现在开始
            withIdentity("myTrigger", "myGroup").
            //withSchedule(scheduleBuilder).build();
            withSchedule(cronScheduleBuilder).build();

    try {
        //1.创建Scheduler工厂
        SchedulerFactory schedulerFactory = new StdSchedulerFactory();
        //2.获取实例
        Scheduler scheduler = schedulerFactory.getScheduler();
        //3.设置jobDetail详情和trigger触发器
        scheduler.scheduleJob(jobDetail, trigger);
        //4.定时任务开始
        scheduler.start();
    } catch (SchedulerException e) {
        e.printStackTrace();
    }

}
```

在项目启动的时候调用 task 方法即可启动定时任务。

## Spring Boot 创建定时任务

Spring Boot 默认已经实现了定时任务，只需要添加相应的注解即可完成

### pom.xml 文件配置

pom.xml 不需要添加其他依赖，只需要加入 Spring Boot 依赖即可，这里我们添加一个 `web` 和 `test` 的依赖

```xml
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
     <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
	</dependency>
</dependencies>
```

### 在启动类上面加上 @EnableScheduling 注解


在启动类上面加上 `@EnableScheduling` 注解即可开启定时任务
```java
@EnableScheduling
@SpringBootApplication
public class SchedulingApplication {

	public static void main(String[] args) {
		SpringApplication.run(SchedulingApplication.class, args);
	}
}
```

### 编写定时任务

```java
@Component
public class ScheduledTask {

    @Scheduled(initialDelay=1000, fixedDelay = 1000)
    public void task1() {
        System.out.println("延迟1000毫秒后执行，任务执行完1000毫秒之后执行！");
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    @Scheduled(fixedRate = 2000)
    public void task2() {
        System.out.println("延迟1000毫秒后执行，之后每2000毫秒执行一次！");
    }

}
```

除了这些还支持 cron 表达式

```java
@Scheduled(cron = "*/2 * * * * ?")
public void task3() {
    System.out.println("每2秒执行一次！");
}
```

启动 Spring Boot 项目在控制台就会看到任务定时执行

## cron 表达式

以下是 cron 表达式的的两种语法
```
Seconds Minutes Hours DayofMonth Month DayofWeek Year
Seconds Minutes Hours DayofMonth Month DayofWeek
```

每一个域可出现的字符如下： 

```
Seconds:可出现", - * /"四个字符，有效范围为0-59的整数 
Minutes:可出现", - * /"四个字符，有效范围为0-59的整数 
Hours:可出现", - * /"四个字符，有效范围为0-23的整数 
DayofMonth:可出现", - * / ? L W C"八个字符，有效范围为0-31的整数 
Month:可出现", - * /"四个字符，有效范围为1-12的整数或JAN-DEc 
DayofWeek:可出现", - * / ? L C #"四个字符，有效范围为1-7的整数或SUN-SAT两个范围。1表示星期天，2表示星期一， 依次类推 
Year:可出现", - * /"四个字符，有效范围为1970-2099年
```

举几个例子

```
*/2 * * * * ?  表示每2秒执行一次！
0 0 2 1 * ? *  表示在每月的1日的凌晨2点调度任务 
0 15 10 ? * MON-FRI 表示周一到周五每天上午10：15执行作业 
0 15 10 ? 6L 2002-2006 表示2002-2006年的每个月的最后一个星期五上午10:15执行作
```

推荐一个 cron 表达式在线生成工具

<http://cron.qqe2.com/>

## 参考资料


本文所有代码放在 [Github](https://github.com/renguangli/spring-boot-samples/tree/master/spring-boot-scheduling) 上  

