---

title: SpringBoot笔记系列：（十一）SpringBoot整合多数据源

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: 43d818bd

date: 2018-07-16 14:14:53

---

#### SpringBoot整合多数据源，多数据源如何区分使用哪个数据源? ####

1. 分包结构
	1. top.pcstar.test1.mapper --- 访问test1数据库
	2. top.pcstar.test2.mapper --- 访问test2数据库
2. 使用注解方式

```java
public class TestServiceImpl implements TestService {
	@datasourcetest001(自定义注解)
	public void test001(){}
	@datasourcetest002(自定义注解)
	public void test002(){}
}
```

<!-- more -->

#### 1.pom文件引入 ####

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.21</version>
</dependency>

<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.1.1</version>
</dependency>
```

#### 2.application.properties新增两个数据源 ####

```properties
#数据库连接设置(数据源1)
spring.datasource.test1.url=jdbc:mysql://127.0.0.1:3306/test1?useSSL=false&serverTimezone=UTC&characterEncoding=utf-8
spring.datasource.test1.username=test1
spring.datasource.test1.password=888888
spring.datasource.test1.driver-class-name=com.mysql.jdbc.Driver
#数据库连接设置(数据源2)
spring.datasource.test2.url=jdbc:mysql://127.0.0.1:3306/test?useSSL=false&serverTimezone=UTC&characterEncoding=utf-8
spring.datasource.test2.username=test
spring.datasource.test2.password=888888
spring.datasource.test2.driver-class-name=com.mysql.jdbc.Driver
```

#### 3.新增两个数据源配置类(DataSource1Config和DataSource2Config) ####

top.pcstar.springbootdatasource.datasource.DataSource1Config

```java
package top.pcstar.springbootdatasource.datasource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.autoconfigure.jdbc.DataSourceProperties;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;

import javax.sql.DataSource;

/**
 * @Author: PanChao
 * @Description: DataSource1Config
 * @Date: Created in 15:15 2018/7/16
 */
@MapperScan(basePackages = "top.pcstar.springbootdatasource.test1",sqlSessionFactoryRef = "test1SqlSessionFactory")
@Configuration
public class DataSource1Config {
    /**
     * 获取DataSourceProperties配置文件
     * @return
     */
    @Primary
    @Bean("test1DataSourceProperties")
    @ConfigurationProperties("spring.datasource.test1")
    public DataSourceProperties getDataSourceProperties(){
        return new DataSourceProperties();
    }
    /**
     * 创建数据源
     *
     * @return
     */
    @Primary
    @Bean("test1DataSource")
    public DataSource getDataSource() {
        return getDataSourceProperties().initializeDataSourceBuilder().build();
    }

    /**
     * test1 sql会话工厂
     *
     * @param test1DataSource
     * @return
     * @throws Exception
     */
    @Primary
    @Bean("test1SqlSessionFactory")
    public SqlSessionFactory getSqlSessionFactory(@Qualifier("test1DataSource") DataSource test1DataSource) throws Exception {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(test1DataSource);
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver()
                .getResources("classpath:mapper/test1/*.xml"));
        return sqlSessionFactoryBean.getObject();
    }

    /**
     * test1 事务管理
     *
     * @param test1DataSource
     * @return
     */
    @Primary
    @Bean("test1DataSourceTransactionManager")
    public DataSourceTransactionManager getDataSourceTransactionManager(@Qualifier("test1DataSource") DataSource test1DataSource) {
        return new DataSourceTransactionManager(test1DataSource);
    }

    /**
     * test1 sql会话模板
     * @param test1SqlSessionFactory
     * @return
     */
    @Primary
    @Bean("test1SqlSessionTemplate")
    public SqlSessionTemplate getSqlSessionTemplate(@Qualifier("test1SqlSessionFactory") SqlSessionFactory test1SqlSessionFactory) {
        return new SqlSessionTemplate(test1SqlSessionFactory);
    }
}
```

top.pcstar.springbootdatasource.datasource.DataSource2Config

```java
package top.pcstar.springbootdatasource.datasource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.autoconfigure.jdbc.DataSourceProperties;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;

import javax.sql.DataSource;

/**
 * @Author: PanChao
 * @Description: DataSource2Config
 * @Date: Created in 15:15 2018/7/16
 */
@MapperScan(basePackages = "top.pcstar.springbootdatasource.test2", sqlSessionFactoryRef = "test2SqlSessionFactory")
@Configuration
public class DataSource2Config {
    /**
     * 获取DataSourceProperties配置文件
     *
     * @return
     */
    @Bean("test2DataSourceProperties")
    @ConfigurationProperties("spring.datasource.test2")
    public DataSourceProperties getDataSourceProperties() {
        return new DataSourceProperties();
    }

    /**
     * 创建数据源
     *
     * @return
     */
    @Bean("test2DataSource")
    public DataSource getDataSource() {
        return getDataSourceProperties().initializeDataSourceBuilder().build();
    }

    /**
     * test2 sql会话工厂
     *
     * @param test2DataSource
     * @return
     * @throws Exception
     */
    @Bean("test2SqlSessionFactory")
    public SqlSessionFactory getSqlSessionFactory(@Qualifier("test2DataSource") DataSource test2DataSource) throws Exception {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(test2DataSource);
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver()
                .getResources("classpath:mapper/test2/*.xml"));
        return sqlSessionFactoryBean.getObject();
    }

    /**
     * test2 事务管理
     *
     * @param test2DataSource
     * @return
     */
    @Bean("test2DataSourceTransactionManager")
    public DataSourceTransactionManager getDataSourceTransactionManager(@Qualifier("test2DataSource") DataSource test2DataSource) {
        return new DataSourceTransactionManager(test2DataSource);
    }

    /**
     * test2 sql会话模板
     *
     * @param test2SqlSessionFactory
     * @return
     */
    @Bean("test2SqlSessionTemplate")
    public SqlSessionTemplate getSqlSessionTemplate(@Qualifier("test2SqlSessionFactory") SqlSessionFactory test2SqlSessionFactory) {
        return new SqlSessionTemplate(test2SqlSessionFactory);
    }
}
```

#### 4.创建分包mapper的xml ####

resources/mapper/test1/test1mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="top.pcstar.springbootdatasource.test1.mapper.Test1Mapper" >
    <select id="findByName" parameterType="java.lang.String" resultType="top.pcstar.springbootdatasource.entity.Test">
        SELECT * FROM TEST WHERE NAME = #{name}
    </select>
    <insert id="insert" parameterType="java.lang.String">
        INSERT INTO TEST(NAME, SEX) VALUES(#{name}, #{sex})
    </insert>
</mapper>
```

resources/mapper/test2/test2mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="top.pcstar.springbootdatasource.test2.mapper.Test2Mapper" >
    <select id="findByName" parameterType="java.lang.String" resultType="top.pcstar.springbootdatasource.entity.Test">
        SELECT * FROM TEST WHERE NAME = #{name}
    </select>
    <insert id="insert" parameterType="java.lang.String">
        INSERT INTO TEST(NAME, SEX) VALUES(#{name}, #{sex})
    </insert>
</mapper>
```

#### 5.创建分包mapper的接口 ####

top.pcstar.springbootdatasource.test1.mapper.Test1Mapper

```java
package top.pcstar.springbootdatasource.test1.mapper;

import org.apache.ibatis.annotations.Param;
import org.springframework.stereotype.Repository;
import top.pcstar.springbootdatasource.entity.Test;

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

top.pcstar.springbootdatasource.test2.mapper.Test2Mapper

```java
package top.pcstar.springbootdatasource.test2.mapper;

import org.apache.ibatis.annotations.Param;
import org.springframework.stereotype.Repository;
import top.pcstar.springbootdatasource.entity.Test;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 16:31 2018/7/17
 */
@Repository
public interface Test2Mapper {
    Test findByName(@Param("name") String name);

    int insert(@Param("name") String name, @Param("sex") String sex);
}
```

#### 6.Service测试类 ####

```java
package top.pcstar.springbootdatasource.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import top.pcstar.springbootdatasource.entity.Test;
import top.pcstar.springbootdatasource.service.TestService;
import top.pcstar.springbootdatasource.test1.mapper.Test1Mapper;
import top.pcstar.springbootdatasource.test2.mapper.Test2Mapper;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 16:36 2018/7/17
 */
@Service
public class TestServiceImpl implements TestService {
    @Autowired
    private Test1Mapper test1Mapper;
    @Autowired
    private Test2Mapper test2Mapper;

    @Override
    public Test getTest1ByName(String name) {
        System.out.println("getTest1ByName");
        return test1Mapper.findByName(name);
    }

    @Override
    public int addTest1(String name, String sex) {
        System.out.println("addTest1");
        return test1Mapper.insert(name, sex);
    }

    @Override
    public Test getTest2ByName(String name) {
        System.out.println("getTest2ByName");
        return test2Mapper.findByName(name);
    }

    @Override
    public int addTest2(String name, String sex) {
        System.out.println("addTest2");
        return test2Mapper.insert(name, sex);
    }
}
```

#### 7.Controller测试类 ####

```java
package top.pcstar.springbootdatasource.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import top.pcstar.springbootdatasource.entity.Test;
import top.pcstar.springbootdatasource.service.TestService;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 16:37 2018/7/17
 */
@RestController
public class TestController {
    @Autowired
    private TestService testService;

    @GetMapping("/getTest1ByName")
    public Test getTest1ByName(String name) {
        System.out.println("getTest1ByName");
        return testService.getTest1ByName(name);
    }

    @GetMapping("/addTest1")
    public void addTest1(String name, String sex) {
        System.out.println("addTest1");
        testService.addTest1(name, sex);
    }

    @GetMapping("/getTest2ByName")
    public Test getTest2ByName(String name) {
        System.out.println("getTest2ByName");
        return testService.getTest2ByName(name);
    }

    @GetMapping("/addTest2")
    public void addTest2(String name, String sex) {
        System.out.println("addTest2");
        testService.addTest2(name, sex);
    }
}
```
[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
