---

title: SpringBoot笔记系列：（十八）异步调用

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: 2747a3ae

date: 2018-07-22 19:59:55

---

SpringBoot中使用异步调用

<!-- more -->

1. 在Spring Boot的启动类中加入@EnableAsync注解，启用异步调用配置

	```java
	package top.pcstar.springboothelloworld;
	
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;
	import org.springframework.scheduling.annotation.EnableAsync;
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
	@EnableAsync
	public class App {
	    public static void main(String[] args) {
	        SpringApplication.run(App.class, args);
	    }
	}
	```

2. 在需要异步执行的方法上加入@Async注解

	```java
	package top.pcstar.springboothelloworld.service.impl;
	
	import org.springframework.scheduling.annotation.Async;
	import org.springframework.stereotype.Service;
	import top.pcstar.springboothelloworld.service.AsyncService;
	
	import java.util.concurrent.TimeUnit;
	
	/**
	 * @Author: PanChao
	 * @Description:
	 * @Date: Created in 20:09 2018/7/22
	 */
	@Service
	public class AsyncServiceImpl implements AsyncService {
	    @Async
	    @Override
	    public String asyncMethod() throws InterruptedException {
	        System.out.println("asyncMethod开始");
	        TimeUnit.SECONDS.sleep(1);
	        System.out.println("asyncMethod结束");
	        return "success";
	    }
	}
	```

3. 在Controller中调用异步方法

	```java
	package top.pcstar.springboothelloworld.controller;
	
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RestController;
	import top.pcstar.springboothelloworld.service.AsyncService;
	
	/**
	 * @Author: PanChao
	 * @Description:
	 * @Date: Created in 20:02 2018/7/22
	 */
	@RestController
	public class AsyncController {
	    @Autowired
	    private AsyncService asyncMethod;
	    @RequestMapping("testAsync")
	    public String testAsync(){
	        System.out.println("testAsync开始");
	        try {
	            asyncMethod.asyncMethod();
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }
	        System.out.println("testAsync结束");
	        return "success";
	    }
	}
	```

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
