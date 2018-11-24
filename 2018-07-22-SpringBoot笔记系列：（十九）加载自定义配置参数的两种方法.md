---

title: SpringBoot笔记系列：（十九）加载自定义配置参数的两种方法

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: 198cc1

date: 2018-07-22 20:19:55

---

SpringBoot中读取application.properties自定义配置参数的两种方法  

<!-- more -->

1. 使用@Value读取单个参数
2. 使用@ConfigurationProperties读取多个参数并封装到bean中(需要在启动类中加上@EnableConfigurationProperties注解)

例如：

1. application.properties文件内容

	```properties
	person.name=LiMing
	person.sex=男
	```

2. Person.java内容

	```java
	package top.pcstar.springboothelloworld.entity;
	
	import org.springframework.boot.context.properties.ConfigurationProperties;
	
	/**
	 * @Author: PanChao
	 * @Description:
	 * @Date: Created in 20:25 2018/7/22
	 */
	@ConfigurationProperties("person")
	public class Person {
	    private String name;
	    private String sex;
	
	    public String getName() {
	        return name;
	    }
	
	    public void setName(String name) {
	        this.name = name;
	    }
	
	    public String getSex() {
	        return sex;
	    }
	
	    public void setSex(String sex) {
	        this.sex = sex;
	    }
	
	    @Override
	    public String toString() {
	        return "Person{" +
	                "name='" + name + '\'' +
	                ", sex='" + sex + '\'' +
	                '}';
	    }
	}
	```

3. ReadparamController中查看是否读取成功

	```java
	package top.pcstar.springboothelloworld.controller;
	
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.beans.factory.annotation.Value;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RestController;
	import top.pcstar.springboothelloworld.entity.Person;
	
	/**
	 * @Author: PanChao
	 * @Description:
	 * @Date: Created in 20:23 2018/7/22
	 */
	@RestController
	public class ReadparamController {
	    @Value("${person.name}")
	    private String name;
	    @Value("${person.sex}")
	    private String sex;
	    @Autowired
	    private Person person;
	    @RequestMapping("/read")
	    public String read(){
	        return "name="+name+"---sex="+sex;
	    }
	    @RequestMapping("/read1")
	    public String read1(){
	        return person.toString();
	    }
	}
	```

4. 启动类中加入@EnableConfigurationProperties注解

    ```java
    package top.pcstar.springboothelloworld;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.scheduling.annotation.EnableAsync;
    import org.springframework.scheduling.annotation.EnableScheduling;
    import top.pcstar.springboothelloworld.entity.Person;
    
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
    @EnableConfigurationProperties(Person.class)
    public class App {
        public static void main(String[] args) {
            SpringApplication.run(App.class, args);
        }
    }
    ```

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
