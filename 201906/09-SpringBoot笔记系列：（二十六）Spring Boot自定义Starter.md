Spring Boot的四大特性：
1. Starter添加项目依赖
2. bean的自动化配置
3. Spring Boot CLI与Groovy的高效配合
4. Spring Boot Actuator

本文讲解如何创建一个自定义Starter。

### 1.创建maven工程(maven-archetype-quickstart)：

注意artifactId的命名规则，Spring官方Starter通常命名为spring-boot-starter-{name}如 spring-boot-starter-web， Spring官方建议非官方Starter命名应遵循{name}-spring-boot-starter的格式, 如mybatis-spring-boot-starter。这里创建的项目的artifactId为hello-spring-boot-starter。

### 2.添加依赖jar包：

**注意**：这里的packaging为jar，starter需要使用到Spring boot的自动配置功能，所以需要引入自动配置相关的依赖

```xml
<dependencies>
    <!-- springboot的spring-boot-autoconfigure模块通过灵活的Auto-configuration注解使SpringBoot中的功能实现模块化和可被替换扩展 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-autoconfigure</artifactId>
        <version>2.0.0.RELEASE</version>
    </dependency>
    <!-- spring默认使用yml中的配置，但有时候要用传统的xml或properties配置，就需要使用spring-boot-configuration-processor了 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <version>2.0.0.RELEASE</version>
        <optional>true</optional>
    </dependency>
</dependencies>
```

### 3.配置自定义属性类PersonProperties.java

在使用Spring官方的Starter时通常可以在application.properties中来配置参数覆盖掉默认的值，例如在使用redis时一般就会有对应的RedisProperties

```java
package com.pcgrw.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;

/**
 * @ClassName PersonProperties
 * @Description 自定义属性类
 * @Author panchao
 * @Date 2019/6/9 12:35
 * @Version 1.0
 */
@Data
@ConfigurationProperties(prefix = "custom.person")
public class PersonProperties {
    private String name;
    private int age;
    private String sex = "M";
}
```

### 4.配置一个自定义服务类bean(PersonService.java)：

每个starter都有自己的功能，例如在spring-boot-starter-jdbc中最重要的类时JdbcTemplate，每个starter中的核心业务类明白都不同，也没什么规律（像spring-boot-starter-data-xxx的命名是比较有规律的），这里使用PersonService来定义hello-spring-boot-starter的功能，这里通过一个sayHello来模拟一个功能。

```java
package com.pcgrw.service;

import com.pcgrw.config.PersonProperties;

/**
 * @ClassName PersonService
 * @Description PersonService
 * @Author panchao
 * @Date 2019/6/9 12:42
 * @Version 1.0
 */
public class PersonService {
    private PersonProperties properties;

    public PersonService() {
    }

    public PersonService(PersonProperties properties) {
        this.properties = properties;
    }

    public void sayHello() {
        System.out.println("大家好，我叫: " + properties.getName() + ", 今年" + properties.getAge() + "岁"
                + ", 性别: " + properties.getSex());
    }
}
```

### 5.自动配置类PersonServiceAutoConfiguration.java：

一般每个starter都至少会有一个自动配置类，一般命名规则使用XxxAutoConfiguration, 例如RedisAutoConfiguration；

这里我们定义自己的自动配置PersonServiceAutoConfiguration，并将核心功能类PersonService放入到Spring Context容器中；

```java
package com.pcgrw.config;

import com.pcgrw.service.PersonService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @ClassName PersonServiceAutoConfiguration
 * @Description 配置文件
 * @Author panchao
 * @Date 2019/6/9 12:44
 * @Version 1.0
 */
@Configuration
@EnableConfigurationProperties(PersonProperties.class)
@ConditionalOnClass(PersonService.class) //当类路径classpath下有指定的类的情况下进行自动配置
@ConditionalOnProperty(prefix = "custom.person", value = "enabled", matchIfMissing = true) //当配置文件中custom.person.enabled=true时进行自动配置，如果没有设置此值就默认使用matchIfMissing对应的值
public class PersonServiceAutoConfiguration {
    @Autowired
    private PersonProperties properties;

    public PersonServiceAutoConfiguration() {
    }

    @Bean
    @ConditionalOnMissingBean  // 当容器中没有指定Bean的情况下，自动配置PersonService类
    public PersonService getPersonService() {
        PersonService personService = new PersonService(properties);
        return personService;
    }
}
```
@Conditional条件注解的含义：

- @ConditionalOnClass：当类路径classpath下有指定的类的情况下进行自动配置
- @ConditionalOnMissingBean:当容器(Spring Context)中没有指定Bean的情况下进行自动配置
- @ConditionalOnProperty(prefix = “example.service”, value = “enabled”, matchIfMissing = true)，当配置文件中example.service.enabled=true时进行自动配置，如果没有设置此值就默认使用matchIfMissing对应的值
- @ConditionalOnMissingBean，当Spring Context中不存在该Bean时。
- @ConditionalOnBean:当容器(Spring Context)中有指定的Bean的条件下
- @ConditionalOnMissingClass:当类路径下没有指定的类的条件下
- @ConditionalOnExpression:基于SpEL表达式作为判断条件
- @ConditionalOnJava:基于JVM版本作为判断条件
- @ConditionalOnJndi:在JNDI存在的条件下查找指定的位置
- @ConditionalOnNotWebApplication:当前项目不是Web项目的条件下
- @ConditionalOnWebApplication:当前项目是Web项目的条件下
- @ConditionalOnResource:类路径下是否有指定的资源
- @ConditionalOnSingleCandidate:当指定的Bean在容器中只有一个，或者在有多个Bean的情况下，用来指定首选的Bean

### 6.在`src/main/resources/META-INF/`路径下创建`spring.factories`文件：

**注意：META-INF是自己手动创建的目录，spring.factories也是手动创建的文件,在该文件中配置自己的自动配置类**

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.pcgrw.config.PersonServiceAutoConfiguration
```

### 7.使用maven命令打包`mvn clean install`

### 8.创建一个新Spring Boot项目，并引入依赖：

```xml
<dependency>
    <groupId>com.pcgrw</groupId>
    <artifactId>hello-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

### 9.配置application.yaml文件：

```yaml
custom:
  person:
    name: 小明
    age: 18
    sex: 男
```

### 10.编写测试案例进行测试：

```java
package com.pcgrw.customstartertest;
import com.pcgrw.service.PersonService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringBootCustomStarterTestApplicationTests {
    @Autowired
    private PersonService personService;
    @Test
    public void contextLoads() {
        personService.sayHello();
    }
}
```

### 总结Starter的工作原理：

1. Spring Boot在启动时扫描项目所依赖的JAR包，寻找包含spring.factories文件的JAR包，
2. 然后读取spring.factories文件获取配置的自动配置类AutoConfiguration，
3. 然后将自动配置类下满足条件(@ConditionalOnXxx)的@Bean放入到Spring容器中(Spring Context)
4. 这样使用者就可以直接用来注入，因为该类已经在容器中了