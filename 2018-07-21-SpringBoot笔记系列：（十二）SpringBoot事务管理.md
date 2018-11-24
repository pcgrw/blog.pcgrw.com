---

title: SpringBoot笔记系列：（十二）SpringBoot事务管理

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: '89013595'

date: 2018-07-21 19:36:06

---

### springboot整合事物管理 ###

1. Spring事务分类
	1. 编程事务(以硬编码的形式进行事务的提交、回滚)
	2. 声明事务(xml方式、注解方式)
2. springboot默认集成事务,只要在方法上加上@Transactional即可;
3. 在多数据源情况下，需要指定使用哪一个事务进行管理：@Transactional(transactionManager = "test1DataSourceTransactionManager");

<!-- more -->

```java
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
```

```java
@Transactional(transactionManager = "test1DataSourceTransactionManager")
@Override
public int addTest1(String name, String sex) {
    System.out.println("addTest1");
    int result = test1Mapper.insert(name, sex);
    addTest2(name, sex);
//        int i = 1/0;
    return result;
}
```

```java
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
```

```java
@Transactional(transactionManager = "test2DataSourceTransactionManager")
@Override
public int addTest2(String name, String sex) {
    System.out.println("addTest2");
    int result = test2Mapper.insert(name, sex);
//        int i = 1/0;
    return result;
}
```

[springboot事务源码](https://github.com/pcstartop/springboot/tree/master/springboot-datasource)

### SpringBoot分布式事物管理(atomikos) ###

分布式事物管理jta+atomikos,将test数据库和test1数据库数据源事务注册第三方atomikos，使用2pc(两阶段提交协议),传统项目分布式事务使用2pc,一般不建议使用。

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
<!-- 分布式事物管理 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jta-atomikos</artifactId>
</dependency>
```

#### 2.application.properties新增两个数据源 ####

```properties
#数据库连接设置(数据源1)
mysql.datasource.test1.url=jdbc:mysql://127.0.0.1:3306/test1?useSSL=false&serverTimezone=UTC&characterEncoding=utf-8
mysql.datasource.test1.username=test1
mysql.datasource.test1.password=888888
mysql.datasource.test1.minPoolSize=3
mysql.datasource.test1.maxPoolSize=25
mysql.datasource.test1.maxLifetime=20000
mysql.datasource.test1.borrowConnectionTimeout=30
mysql.datasource.test1.loginTimeout=30
mysql.datasource.test1.maintenanceInterval=60
mysql.datasource.test1.maxIdleTime=60
mysql.datasource.test1.testQuery=select 1
#数据库连接设置(数据源2)
mysql.datasource.test2.url=jdbc:mysql://127.0.0.1:3306/test?useSSL=false&serverTimezone=UTC&characterEncoding=utf-8
mysql.datasource.test2.username=test
mysql.datasource.test2.password=888888
mysql.datasource.test2.minPoolSize=3
mysql.datasource.test2.maxPoolSize=25
mysql.datasource.test2.maxLifetime=20000
mysql.datasource.test2.borrowConnectionTimeout=30
mysql.datasource.test2.loginTimeout=30
mysql.datasource.test2.maintenanceInterval=60
mysql.datasource.test2.maxIdleTime=60
mysql.datasource.test2.testQuery=select 1
```

#### 3.读取数据源配置文件到bean中 ####

top.pcstar.springbootdatasource.config.DBConfig1

```java
package top.pcstar.springbootdatasource.config;

import org.springframework.boot.context.properties.ConfigurationProperties;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 22:00 2018/7/21
 */
@ConfigurationProperties("mysql.datasource.test1")
public class DBConfig1 {
    private String url;
    private String username;
    private String password;
    private int minPoolSize;
    private int maxPoolSize;
    private int maxLifetime;
    private int borrowConnectionTimeout;
    private int loginTimeout;
    private int maintenanceInterval;
    private int maxIdleTime;
    private String testQuery;

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public int getMinPoolSize() {
        return minPoolSize;
    }

    public void setMinPoolSize(int minPoolSize) {
        this.minPoolSize = minPoolSize;
    }

    public int getMaxPoolSize() {
        return maxPoolSize;
    }

    public void setMaxPoolSize(int maxPoolSize) {
        this.maxPoolSize = maxPoolSize;
    }

    public int getMaxLifetime() {
        return maxLifetime;
    }

    public void setMaxLifetime(int maxLifetime) {
        this.maxLifetime = maxLifetime;
    }

    public int getBorrowConnectionTimeout() {
        return borrowConnectionTimeout;
    }

    public void setBorrowConnectionTimeout(int borrowConnectionTimeout) {
        this.borrowConnectionTimeout = borrowConnectionTimeout;
    }

    public int getLoginTimeout() {
        return loginTimeout;
    }

    public void setLoginTimeout(int loginTimeout) {
        this.loginTimeout = loginTimeout;
    }

    public int getMaintenanceInterval() {
        return maintenanceInterval;
    }

    public void setMaintenanceInterval(int maintenanceInterval) {
        this.maintenanceInterval = maintenanceInterval;
    }

    public int getMaxIdleTime() {
        return maxIdleTime;
    }

    public void setMaxIdleTime(int maxIdleTime) {
        this.maxIdleTime = maxIdleTime;
    }

    public String getTestQuery() {
        return testQuery;
    }

    public void setTestQuery(String testQuery) {
        this.testQuery = testQuery;
    }
}
```

top.pcstar.springbootdatasource.config.DBConfig2

```java
package top.pcstar.springbootdatasource.config;

import org.springframework.boot.context.properties.ConfigurationProperties;

/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 22:00 2018/7/21
 */
@ConfigurationProperties("mysql.datasource.test2")
public class DBConfig2 {
    private String url;
    private String username;
    private String password;
    private int minPoolSize;
    private int maxPoolSize;
    private int maxLifetime;
    private int borrowConnectionTimeout;
    private int loginTimeout;
    private int maintenanceInterval;
    private int maxIdleTime;
    private String testQuery;

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public int getMinPoolSize() {
        return minPoolSize;
    }

    public void setMinPoolSize(int minPoolSize) {
        this.minPoolSize = minPoolSize;
    }

    public int getMaxPoolSize() {
        return maxPoolSize;
    }

    public void setMaxPoolSize(int maxPoolSize) {
        this.maxPoolSize = maxPoolSize;
    }

    public int getMaxLifetime() {
        return maxLifetime;
    }

    public void setMaxLifetime(int maxLifetime) {
        this.maxLifetime = maxLifetime;
    }

    public int getBorrowConnectionTimeout() {
        return borrowConnectionTimeout;
    }

    public void setBorrowConnectionTimeout(int borrowConnectionTimeout) {
        this.borrowConnectionTimeout = borrowConnectionTimeout;
    }

    public int getLoginTimeout() {
        return loginTimeout;
    }

    public void setLoginTimeout(int loginTimeout) {
        this.loginTimeout = loginTimeout;
    }

    public int getMaintenanceInterval() {
        return maintenanceInterval;
    }

    public void setMaintenanceInterval(int maintenanceInterval) {
        this.maintenanceInterval = maintenanceInterval;
    }

    public int getMaxIdleTime() {
        return maxIdleTime;
    }

    public void setMaxIdleTime(int maxIdleTime) {
        this.maxIdleTime = maxIdleTime;
    }

    public String getTestQuery() {
        return testQuery;
    }

    public void setTestQuery(String testQuery) {
        this.testQuery = testQuery;
    }
}
```

#### 4.启动类添加注解 ####

	@EnableConfigurationProperties(value = { DBConfig1.class, DBConfig2.class })

#### 5.新增两个数据源配置类(MyBatisConfig1和MyBatisConfig2) ####

top.pcstar.springbootdatasource.datasource.MyBatisConfig1

```java
package top.pcstar.springbootdatasource.datasource;

import com.mysql.jdbc.jdbc2.optional.MysqlXADataSource;
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.jta.atomikos.AtomikosDataSourceBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import top.pcstar.springbootdatasource.config.DBConfig1;

import javax.sql.DataSource;
import java.sql.SQLException;

/**
 * @Author: PanChao
 * @Description: DataSource1Config
 * @Date: Created in 15:15 2018/7/16
 */
@MapperScan(basePackages = "top.pcstar.springbootdatasource.test1", sqlSessionFactoryRef = "test1SqlSessionFactory")
@Configuration
public class MyBatisConfig1 {
    /**
     * 创建数据源
     *
     * @return
     */
    @Primary
    @Bean("test1DataSource")
    public DataSource getDataSource(DBConfig1 dbConfig1) throws SQLException {
        MysqlXADataSource mysqlXaDataSource = new MysqlXADataSource();
        mysqlXaDataSource.setUrl(dbConfig1.getUrl());
        mysqlXaDataSource.setPinGlobalTxToPhysicalConnection(true);
        mysqlXaDataSource.setPassword(dbConfig1.getPassword());
        mysqlXaDataSource.setUser(dbConfig1.getUsername());
        mysqlXaDataSource.setPinGlobalTxToPhysicalConnection(true);

        AtomikosDataSourceBean xaDataSource = new AtomikosDataSourceBean();
        xaDataSource.setXaDataSource(mysqlXaDataSource);
        xaDataSource.setUniqueResourceName("test1DataSource");

        xaDataSource.setMinPoolSize(dbConfig1.getMinPoolSize());
        xaDataSource.setMaxPoolSize(dbConfig1.getMaxPoolSize());
        xaDataSource.setMaxLifetime(dbConfig1.getMaxLifetime());
        xaDataSource.setBorrowConnectionTimeout(dbConfig1.getBorrowConnectionTimeout());
        xaDataSource.setLoginTimeout(dbConfig1.getLoginTimeout());
        xaDataSource.setMaintenanceInterval(dbConfig1.getMaintenanceInterval());
        xaDataSource.setMaxIdleTime(dbConfig1.getMaxIdleTime());
        xaDataSource.setTestQuery(dbConfig1.getTestQuery());
        return xaDataSource;
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
     * test1 sql会话模板
     *
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

top.pcstar.springbootdatasource.datasource.MyBatisConfig2

```java
package top.pcstar.springbootdatasource.datasource;

import com.mysql.jdbc.jdbc2.optional.MysqlXADataSource;
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.jta.atomikos.AtomikosDataSourceBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import top.pcstar.springbootdatasource.config.DBConfig2;

import javax.sql.DataSource;
import java.sql.SQLException;

/**
 * @Author: PanChao
 * @Description: DataSource2Config
 * @Date: Created in 15:15 2018/7/16
 */
@MapperScan(basePackages = "top.pcstar.springbootdatasource.test2", sqlSessionFactoryRef = "test2SqlSessionFactory")
@Configuration
public class MyBatisConfig2 {
    /**
     * 创建数据源
     *
     * @return
     */
    @Bean("test2DataSource")
    public DataSource getDataSource(DBConfig2 dBConfig2) throws SQLException {
        MysqlXADataSource mysqlXaDataSource = new MysqlXADataSource();
        mysqlXaDataSource.setUrl(dBConfig2.getUrl());
        mysqlXaDataSource.setPinGlobalTxToPhysicalConnection(true);
        mysqlXaDataSource.setPassword(dBConfig2.getPassword());
        mysqlXaDataSource.setUser(dBConfig2.getUsername());
        mysqlXaDataSource.setPinGlobalTxToPhysicalConnection(true);

        AtomikosDataSourceBean xaDataSource = new AtomikosDataSourceBean();
        xaDataSource.setXaDataSource(mysqlXaDataSource);
        xaDataSource.setUniqueResourceName("test2DataSource");

        xaDataSource.setMinPoolSize(dBConfig2.getMinPoolSize());
        xaDataSource.setMaxPoolSize(dBConfig2.getMaxPoolSize());
        xaDataSource.setMaxLifetime(dBConfig2.getMaxLifetime());
        xaDataSource.setBorrowConnectionTimeout(dBConfig2.getBorrowConnectionTimeout());
        xaDataSource.setLoginTimeout(dBConfig2.getLoginTimeout());
        xaDataSource.setMaintenanceInterval(dBConfig2.getMaintenanceInterval());
        xaDataSource.setMaxIdleTime(dBConfig2.getMaxIdleTime());
        xaDataSource.setTestQuery(dBConfig2.getTestQuery());
        return xaDataSource;
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

#### 6.创建分包mapper的xml ####

mapper/test1/test1mapper.xml

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

mapper/test2/test2mapper.xml

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

#### 7.创建分包mapper的接口 ####

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

#### 8.Service测试类 ####

top.pcstar.springbootdatasource.service.impl.TestServiceImpl

```java
@Transactional
@Override
public int addAll(String name, String sex) {
    System.out.println("addAll");
    test1Mapper.insert(name, sex);
    test2Mapper.insert(name, sex);
    int i = 1/0;
    return 1;
}
```

#### 9.Controller测试类 ####

top.pcstar.springbootdatasource.controller.TestController

```java
@GetMapping("/addAll")
public void addAll(String name, String sex) {
    System.out.println("addAll");
    testService.addAll(name, sex);
}
```

#### 10.启动项目 ####

```java
package top.pcstar.springbootdatasource;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import top.pcstar.springbootdatasource.config.DBConfig1;
import top.pcstar.springbootdatasource.config.DBConfig2;

@SpringBootApplication
@EnableConfigurationProperties({DBConfig1.class, DBConfig2.class})
public class SpringbootTransactionApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootTransactionApplication.class, args);
    }
}
```

[SpringBoot分布式事务管理(atomikos)源码](https://github.com/pcstartop/springboot/tree/master/springboot-transaction)

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
