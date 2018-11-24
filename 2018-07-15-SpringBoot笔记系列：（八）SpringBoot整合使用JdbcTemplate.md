---

title: SpringBoot笔记系列：（八）SpringBoot整合使用JdbcTemplate

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: a1203977

date: 2018-07-15 21:25:24

---

#### springboot整合使用JdbcTemplate ####

<!-- more -->

1. pom文件引入

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.21</version>
    </dependency>
    ```

2. application.properties新增配置

    ```properties
    #数据库连接设置
    spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?useSSL=false&serverTimezone=UTC&characterEncoding=utf-8
    spring.datasource.username=test
    spring.datasource.password=888888
    spring.datasource.driver-class-name=com.mysql.jdbc.Driver
    ```

3. Service测试类

    ```java
    package top.pcstar.springboothelloworld.service.impl;
    
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.jdbc.core.JdbcTemplate;
    import org.springframework.stereotype.Service;
    import top.pcstar.springboothelloworld.service.TestService;
    
    /**
     * @Author: PanChao
     * @Description: JdbcTemplate测试
     * @Date: Created in 20:48 2018/7/15
     */
    @Service
    public class TestServiceImpl implements TestService {
        @Autowired
        private JdbcTemplate jdbcTemplate;
        @Override
        public void insterTest(String name,String sex) {
            System.out.println("insterTest开始");
            jdbcTemplate.update("INSERT INTO `test`.`test` (`id`, `name`, `sex`) VALUES (null , ?, ?)", name, sex);
            System.out.println("insterTest结束");
        }
    }
    ```

4. Controller测试类

    ```java
    package top.pcstar.springboothelloworld.controller;
    
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    import top.pcstar.springboothelloworld.service.TestService;
    
    /**
     * @Author: PanChao
     * @Description: Controller测试类
     * @Date: Created in 20:52 2018/7/15
     */
    @RestController
    public class TestController {
        @Autowired
        private TestService testService;
        @RequestMapping("/insterTest")
        public String insterTest(String name,String sex) {
            testService.insterTest(name,sex);
            return "success";
        }
    }
    
    ```

[SpringBoot笔记系列目录](e0c584e.html)

