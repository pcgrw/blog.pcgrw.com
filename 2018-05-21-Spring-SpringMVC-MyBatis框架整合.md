---

title: Spring+SpringMVC+MyBatis框架整合

categories:

- Java
- Framework(框架)

tags:

- Spring
- SpringMVC
- MyBatis
- SSM

abbrlink: d8041df0

date: 2018-05-21 11:17:11

---

SSM（Spring+SpringMVC+MyBatis）框架集由Spring、SpringMVC、MyBatis三个开源框架整合而成，常作为数据源较简单的web项目的框架。

----------

<!-- more -->

### Spring+SpringMVC+MyBatis框架整合步骤: ###

1.[SSM项目创建及依赖jar包](#jump1)  
2.[集成spring-mvn](#jump2)  
3.[集成spring](#jump3)  
4.[集成c3p0](#jump4)  
5.[配置spring声明式事务管理](#jump5)  
6.[集成mybatis](#jump6)  
7.[SSM(Spring+SpringMVC+MyBatis)源码](#jump7)  

<span id="jump1"></span>
### 1. SSM项目创建及依赖jar包

#### 1.1. 使用maven创建一个web项目 ####

#### 1.2. 在pom.xml文件中添加依赖jar包 ####

##### 1.1.1. web工程依赖jar包 #####

jar包 | 作用
---|---
servlet-api | web工程jar包
jstl | java标准标签库
    
##### 1.1.2. spring-mvn依赖jar包 #####
    
jar包 | 作用
---|---
spring-webmvc | spring-mvn依赖jar包
    
##### 1.1.3. spring依赖jar包 #####

Sping依赖的jar包在spring-webmvc中有依赖关系，已经自动导入
    
##### 1.1.4. 数据库依赖jar包 #####
    
jar包 | 作用
---|---
spring-jdbc | 包含对Spring对JDBC数据访问进行封装的所有类
c3p0 | c3p0数据库连接池包
mysql-connector-java | mysql数据库驱动包
    
##### 1.1.5. spring声明式事务管理依赖jar包 #####
    
jar包 | 作用
---|---
spring-tx | spring事务管理jar包
    
##### 1.1.6. mybatis依赖jar包 #####
    
jar包 | 作用
---|---
mybatis | mybatis核心jar包
mybatis-spring | mybatis和spring整合
pagehelper | 分页jar包
cglib | 动态代理类库

#### 1.3. 资源文件 ####

[pom.xml](https://github.com/pcstartop/ssmtest/blob/master/pom.xml)

<span id="jump2"></span>
### 2. 集成spring-mvn

#### 2.1. 在src/main/resources目录下添加spring-mvc.xml配置文件 ####

##### 2.1.1. 添加注解驱动 #####

```xml
<!-- 添加注解驱动器,使用注解来进行请求映射 -->
<mvc:annotation-driven />
```

##### 2.1.2. 注册视图解析器 #####

```xml
<!-- 视图解析器 -->
<bean id="internalResourceViewResolver"
	class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<!-- 重定向时是否添加上下文路径 -->
	<property name="redirectContextRelative" value="true"></property>
	<property name="prefix" value="WEB-INF/views/"></property>
	<property name="suffix" value=".jsp"></property>
</bean>
```

##### 2.1.3. 扫描mvc组件 #####
    
```xml
<!-- 扫描所有的控制器 -->
<context:component-scan base-package="com.panchao.ssmtest.action"></context:component-scan>
```

#### 2.2. 在web.xml中配置spring-mvc前端控制器DispatcherServlet ####

##### 2.2.1. 配置随服务器启动而初始化 #####

##### 2.2.2. 配置参数，指向spring-mvn的路径 #####

##### 2.2.3. 配置servlet-mapping(仅处理*.do请求) #####
    
```xml
<!-- spring-mvc前端控制器DispatcherServlet -->
<servlet>
	<servlet-name>spring-mvc</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring-mvc.xml</param-value>
	</init-param>
</servlet>
<servlet-mapping>
	<servlet-name>spring-mvc</servlet-name>
	<url-pattern>*.do</url-pattern>
</servlet-mapping>
```
   
#### 2.3. 在web.xml中配置请求和应答字符编码处理过滤器CharacterEncodingFilter ####

```xml
<!-- 配置请求和应答字符编码处理过滤器CharacterEncodingFilter -->
<filter>
	<filter-name>encoding-filter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-param>
		<param-name>encoding</param-name>
		<param-value>UTF-8</param-value>
	</init-param>
</filter>
<filter-mapping>
	<filter-name>encoding-filter</filter-name>
	<servlet-name>spring-mvc</servlet-name>
</filter-mapping>
```

#### 2.4. web.xml中配置404,500,欢迎页面等特殊页面 ####

#### 2.5. 编写测试案例 ####

```java
package com.panchao.ssmtest.action;

import java.util.List;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.panchao.ssmtest.entity.Test;
import com.panchao.ssmtest.service.ITestService;

@Controller
public class LoginAction {
    private Logger logger = Logger.getLogger(this.getClass());
    @Autowired
    private ITestService testService;

    @RequestMapping("/LoginAction.login.do")
    public String login(HttpServletRequest request, HttpServletResponse response) {
        logger.info("------------------------------Log4j测试开始......----------------------------------");
        List<Test> list = testService.selectTest();
        for (Test test : list) {
            System.out.println(test);
        }
        logger.error("-------------------测试error---------------------");
        System.out.println("我進來了!");
        logger.info("------------------------------Log4j测试结束----------------------------------");
        return "main";
    }
}
```
#### 2.6. 资源文件 ####

2.6.1.  [spring-mvc.xml](https://github.com/pcstartop/ssmtest/blob/master/src/main/resources/spring-mvc.xml)  
2.6.2. [web.xml](https://github.com/pcstartop/ssmtest/blob/master/src/main/webapp/WEB-INF/web.xml)  
2.6.3. [LoginAction.java](https://github.com/pcstartop/ssmtest/blob/master/src/main/java/com/panchao/ssmtest/action/LoginAction.java)  

<span id="jump3"></span>
### 3. 集成spring

#### 3.1. 在src/main/resources目录下添加spring-context.xml配置文件 ####

##### 3.1.1. 扫描业务层组件 #####

```xml
<!-- 扫描所有业务层组件 -->
<context:component-scan base-package="com.panchao.ssmtest.service"></context:component-scan>
```

#### 3.2. 在web.xml中配置ContextLoaderListener监听器启动spring容器 ####

```xml
<!-- 配置ContextLoaderListener监听器启动spring -->
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

#### 3.3. 在web.xml中配置全局参数contextConfigLocation,指定spring-context.xml的路径 ####

```xml
<!-- 配置全局参数contextConfigLocation,指定spring-context.xml的路径 -->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:spring-context.xml</param-value>
</context-param>
```

#### 3.4. 资源文件 ####

3.4.1. [spring-context.xml](https://github.com/pcstartop/ssmtest/blob/master/src/main/resources/spring-context.xml)  

3.4.2. [web.xml](https://github.com/pcstartop/ssmtest/blob/master/src/main/webapp/WEB-INF/web.xml)  

<span id="jump4"></span>
### 4. 集成c3p0

#### 4.1. 在spring-context.xml中定义c3p0数据源ComboPooledDataSource ####

```xml
<!-- c3p0数据源 -->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
	<property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/test"></property>
	<property name="user" value="test"></property>
	<property name="password" value="888888"></property>
	<property name="minPoolSize" value="1"></property>
	<property name="maxPoolSize" value="5"></property>
	<property name="initialPoolSize" value="1"></property>
	<property name="acquireIncrement" value="1"></property>
</bean>
```

#### 4.2. 资源文件 ####

4.2.1. [spring-context.xml](https://github.com/pcstartop/ssmtest/blob/master/src/main/resources/spring-context.xml)  

<span id="jump5"></span>
### 5. 配置spring声明式事务管理

#### 5.1. 在spring-context.xml配置数据源事务管理DataSourceTransactionManager ####

```xml
<!-- 配置数据源事务管理DataSourceTransactionManager -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"></property>
</bean>
```

#### 5.2. 指定通过注解控制事务 ####

```xml
<!-- 指定通过注解控制事务 -->
<tx:annotation-driven transaction-manager="transactionManager" />
```

#### 5.3. 资源文件 ####

5.3.1. [spring-context.xml](https://github.com/pcstartop/ssmtest/blob/master/src/main/resources/spring-context.xml)

<span id="jump6"></span>
### 6. 集成mybatis

#### 6.1. 编写mybatis配置文件 ####

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 参数设置 -->
	<settings>
		<!-- 这个配置使全局的映射器启用或禁用缓存 -->
		<setting name="cacheEnabled" value="true" />
		<!-- 设置超时时间，它决定驱动等待一个数据库响应的时间 -->
		<setting name="defaultStatementTimeout" value="25000" />
		<!--是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 
			的类似映射。 -->
		<setting name="mapUnderscoreToCamelCase" value="true" />
		<!-- 使用CGLIB代理 -->
		<setting name="proxyFactory" value="CGLIB" />
		<!-- 全局启用或禁用延迟加载。当禁用时，所有关联对象都会即时加载 -->
		<setting name="lazyLoadingEnabled" value="true" />
	</settings>

	<!--plugins插件之 分页拦截器 -->
	<plugins>
		<plugin interceptor="com.github.pagehelper.PageInterceptor">
			<!-- 设置为true时，会将RowBounds第一个参数offset当成pageNum页码使用 -->
			<property name="offsetAsPageNum" value="true"/>
			<!-- 设置为true时，使用RowBounds分页时会进行count查询 -->
			<property name="rowBoundsWithCount" value="true"/>
			<!-- 设置为true时，如果pageSize=0或者RowBounds.limit=0就会查询出全部的结果 -->
			<property name="pageSizeZero" value="true"/>
		</plugin>
	</plugins>
</configuration>
```

#### 6.2. 在spring-context.xml中配置SqlSessionFactoryBean ####

```xml
<!-- spring和MyBatis完美整合 -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<!-- 指定数据源 -->
	<property name="dataSource" ref="dataSource" />
	<!-- 具体指定xml文件，可不配 -->
	<property name="configLocation" value="classpath:mybatis-config.xml" />
	<!-- 自动扫描mapping.xml文件，**表示迭代查找 -->
	<property name="mapperLocations" value="classpath:mapper/*.xml" />
</bean>
```

#### 6.3. 在spring-context.xml中配置扫描mapper生成dao(MapperScannerConfigurer) ####

```xml
<!-- 自动扫描com.panchao.ssmtest.dao下的所有dao接口，并实现这些接口，可直接在程序中使用dao接口，不用再获取sqlsession对象 -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<!-- basePackage 属性是映射器接口文件的包路径。 你可以使用分号或逗号 作为分隔符设置多于一个的包路径 -->
	<property name="basePackage" value="com.panchao.ssmtest.dao" />
	<!-- 因为会自动装配 SqlSessionFactory和SqlSessionTemplate 所以没 有 必 要 去 指 定 SqlSessionFactory或 
		SqlSessionTemplate 因此可省略不配置； 但是,如果你使 用了一个 以上的 DataSource，那么自动装配可能会失效。 这种 
		情况下，你可以使用sqlSessionFactoryBeanName或sqlSessionTemplateBeanName属性来设置正确的 bean名称来使用； -->
	<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
</bean>
```

#### 6.4. 资源文件 ####

6.4.1. [mybatis-config.xml](https://github.com/pcstartop/ssmtest/blob/master/src/main/resources/mybatis-config.xml)  

6.4.2. [spring-context.xml](https://github.com/pcstartop/ssmtest/blob/master/src/main/resources/spring-context.xml)  

<span id="jump7"></span>
### 7. SSM（Spring+SpringMVC+MyBatis）源码

[点击查看源码](https://github.com/pcstartop/ssmtest)
