---

title: SpringBoot笔记系列：（九）SpringBoot整合使用mybatis

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: '79189870'

date: 2018-07-17 15:27:49

---

#### springboot整合使用mybatis ####

<!-- more -->

1.pom文件引入

```xml
<!-- mybatis 依赖 -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.1.1</version>
</dependency>
<!-- mysql 依赖 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

2.application.properties新增配置

```properties
#数据库连接设置
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?useSSL=false&serverTimezone=UTC&characterEncoding=utf-8
spring.datasource.username=test
spring.datasource.password=888888
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

3.Mapper代码

```java
package top.pcstar.springbootmybatis.mapper;

import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;
import top.pcstar.springbootmybatis.entity.Test;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 16:31 2018/7/17
 */
@Repository
public interface TestMapper {
    @Select("SELECT * FROM TEST WHERE NAME = #{name}")
    Test findByName(@Param("name") String name);

    @Insert("INSERT INTO TEST(NAME, SEX) VALUES(#{name}, #{sex})")
    int insert(@Param("name") String name, @Param("sex") String sex);
}
```

4.service代码

```java
package top.pcstar.springbootmybatis.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import top.pcstar.springbootmybatis.entity.Test;
import top.pcstar.springbootmybatis.mapper.TestMapper;
import top.pcstar.springbootmybatis.service.TestService;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 16:36 2018/7/17
 */
@Service
public class TestServiceImpl implements TestService {
    @Autowired
    private TestMapper testMapper;

    @Override
    public Test getTestByName(String name) {
        System.out.println("getTestByName");
        return testMapper.findByName(name);
    }

    @Override
    public int addTest(String name, String sex) {
        return testMapper.insert(name, sex);
    }
}
```

5.controller代码

```java
package top.pcstar.springbootmybatis.contorller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import top.pcstar.springbootmybatis.entity.Test;
import top.pcstar.springbootmybatis.service.TestService;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 16:37 2018/7/17
 */
@RestController
public class TestController {
    @Autowired
    private TestService testService;
    @GetMapping("/getTestByName")
    public Test getTestByName(String name){
        System.out.println("getTestByName");
        return testService.getTestByName(name);
    }
    @GetMapping("/addTest")
    public void addTest(String name,String sex){
        testService.addTest(name, sex);
    }
}
```

6.启动方式

```java
package top.pcstar.springbootmybatis;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@MapperScan("top.pcstar.springbootmybatis.mapper")
@SpringBootApplication
public class SpringbootMybatisApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootMybatisApplication.class, args);
    }
}
```

#### 使用xml文件保存sql语句(需要做如下修改) ####

1.application.properties新增配置

```properties
#mybatis配置
mybatis.mapper-locations=classpath:mapper/*.xml
mybatis.type-aliases-package=top.pcstar.springbootmybatis.entity
```

2.新增mapper的xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="top.pcstar.springbootmybatis.mapper.Test1Mapper" >
    <select id="findByName" parameterType="java.lang.String" resultType="top.pcstar.springbootmybatis.entity.Test">
        SELECT * FROM TEST WHERE NAME = #{name}
    </select>
    <insert id="insert" parameterType="java.lang.String">
        INSERT INTO TEST(NAME, SEX) VALUES(#{name}, #{sex})
    </insert>
</mapper>
```

3.Mapper代码

```java
package top.pcstar.springbootmybatis.mapper;

import org.apache.ibatis.annotations.Param;
import org.springframework.stereotype.Repository;
import top.pcstar.springbootmybatis.entity.Test;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 16:31 2018/7/17
 */
@Repository
public interface Test1Mapper {
    Test findByName(@Param("name") String name);

    int insert(@Param("name") String name, @Param("sex") String sex);
}
```

4.service,controller,启动类保持不变

[SpringBoot笔记系列目录](e0c584e.html)