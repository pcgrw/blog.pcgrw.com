---

title: SpringBoot笔记系列：（十七）创建定时任务

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: 23126d43

date: 2018-07-22 19:44:13

---

SpringBoot中创建定时任务

<!-- more -->

1. 在Spring Boot的启动类中加入@EnableScheduling注解，启用定时任务的配置

	```java
	package top.pcstar.springboothelloworld;
	
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;
	import org.springframework.scheduling.annotation.EnableScheduling;
	
	/**
	 * @Author: PanChao
	 * @Description: 启动类
	 * @Date: Created in 12:56 2018/5/29
	 */
	//@EnableAutoConfiguration
	//@ComponentScan(basePackages = "top.pcstar.springboothelloworld.controller")
	//@EntityScan("top.pcstar.springboothelloworld.entity")
	//@EnableJpaRepositories("top.pcstar.springboothelloworld.dao")
	@SpringBootApplication
	@EnableScheduling
	public class App {
	    public static void main(String[] args) {
	        SpringApplication.run(App.class, args);
	    }
	}
	```

2. 在任务方法上添加@Scheduled注解

	```java
	package top.pcstar.springboothelloworld.task;
	
	import org.springframework.scheduling.annotation.Scheduled;
	import org.springframework.stereotype.Component;
	
	import java.text.SimpleDateFormat;
	import java.util.Date;
	
	/**
	 * @Author: PanChao
	 * @Description:
	 * @Date: Created in 19:29 2018/7/22
	 */
	@Component
	public class ScheduledTasks {
	    @Scheduled(fixedRate = 5000)
	    public void printCurrentTime() {
	        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
	        System.out.println("现在时间：" + simpleDateFormat.format(new Date()));
	    }
	}
	```

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
