---

title: Spring中同一个service中方法相互调用事务不生效问题解决方案

categories:

- Java
- Spring

tags:

- Spring

abbrlink: 544afacd

date: 2018-09-27 12:37:32

---

> 问题描述：我们在用Spring框架开发Web项目过程中，经常需要用同一个service中的一个方法调用另一个方法，如果此时调用方没有添加事务注解@Transactional，而在被调用方添加事务注解@Transactional，当被调用方法中出现异常，这时候会发现事务并没有回滚，事务注解@Transactional没有起作用。

<!-- more -->

### 分析原因： ###
	
我们知道Spring中事务管理是使用AOP代理技术实现的，目标对象自身并没有事务管理功能的，而是通过代理对象动态增强功能对事务进行增强的。因此当我们在同一个service类中通过一个方法调用另一个方法时，是通过目标对象this对象调用的，目标对象自身并没有事务管理功能，因此事务不能生效。

### 解决方案： ###

#### 1.使用xml配置方式暴漏代理对象，然后在service中通过代理对象AopContext.currentProxy()去调用方法。 ####

- xml配置

```xml
<aop:aspectj-autoproxy expose-proxy="true"/>
```

- service调用

```java
@Service
public class HelloWorldServiceImpl implements HelloWorldService {
    @Autowired
    private BlogRepository blogRepository;

    @Override
    public void a(BlogEntity blogEntity) throws Exception {
        ((HelloWorldService) AopContext.currentProxy()).b(blogEntity);
    }

    @Transactional(rollbackFor = Exception.class)
    @Override
    public void b(BlogEntity blogEntity) throws Exception {
        blogRepository.save(blogEntity);
        throw new Exception("错误");
    }
}
```

#### 2.在java配置类上添加注解@EnableAspectJAutoProxy(exposeProxy = true)方式暴漏代理对象，然后在service中通过代理对象AopContext.currentProxy()去调用方法。 ####

- java配置类

```java
/**
 * @Author: PanChao
 * @Description:
 * @Date: Created in 13:14 2018/9/27
 */
@Configuration
@EnableAspectJAutoProxy(exposeProxy = true)
public class AppConfig {
}
```

- service调用

```java
@Service
public class HelloWorldServiceImpl implements HelloWorldService {
    @Autowired
    private BlogRepository blogRepository;

    @Override
    public void a(BlogEntity blogEntity) throws Exception {
        ((HelloWorldService) AopContext.currentProxy()).b(blogEntity);
    }

    @Transactional(rollbackFor = Exception.class)
    @Override
    public void b(BlogEntity blogEntity) throws Exception {
        blogRepository.save(blogEntity);
        throw new Exception("错误");
    }
}
```

#### 3.(不推荐)在service中自动装配service自身，然后同过装配对象调用。 ####

```java
@Service
public class HelloWorldServiceImpl implements HelloWorldService {
    @Autowired
    private BlogRepository blogRepository;
    @Autowired
    private HelloWorldService helloWorldService;

    @Override
    public void a(BlogEntity blogEntity) throws Exception {
        helloWorldService.b(blogEntity);
    }

    @Transactional(rollbackFor = Exception.class)
    @Override
    public void b(BlogEntity blogEntity) throws Exception {
        blogRepository.save(blogEntity);
        throw new Exception("错误");
    }
}
```

通过上述三种方法任意一种即可解决该问题，推荐使用第一二种方法。

