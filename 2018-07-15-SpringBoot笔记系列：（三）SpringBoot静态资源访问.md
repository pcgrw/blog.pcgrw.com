---

title: SpringBoot笔记系列：（三）SpringBoot静态资源访问

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: 1e196e38

date: 2018-07-15 11:04:35

---

> 在我们开发Web应用的时候，需要引用大量的js、css、图片等静态资源。

<!-- more -->

## 一、默认静态资源映射

Spring Boot默认提供静态资源目录位置需置于classpath下，目录名需符合如下规则(Spring Boot 默认将 /** 所有访问映射到以下目录)：

- resources/static
- resources/public
- resources/resources
- resources/META-INF/resources

> 举例：我们可以在src/main/resources/目录下创建static，在该位置放置一个图片文件。启动程序后，尝试访问 http://localhost:8080/tupian.jpg ，如能显示图片，配置成功。

## 二、自定义静态资源映射

静态资源路径是指系统可以直接访问的路径，且路径下的所有文件均可被用户直接读取。

在Springboot中默认的静态资源路径有：`classpath:/META-INF/resources/，classpath:/resources/，classpath:/static/，classpath:/public/`，从这里可以看出这里的静态资源路径都是在classpath中（也就是在项目路径下指定的这几个文件夹）

试想这样一种情况：一个网站有文件上传文件的功能，如果被上传的文件放在上述的那些文件夹中会有怎样的后果？

- 网站数据与程序代码不能有效分离；
- 当项目被打包成一个.jar文件部署时，再将上传的文件放到这个.jar文件中是有多么低的效率；
- 网站数据的备份将会很痛苦。

此时可能最佳的解决办法是将静态资源路径设置到磁盘的基本个目录。

### 第一种方式：

实现WebMvcConfigurer接口，实现addResourceHandlers()方法；

配置如下：
```java
package com.pcgrw.staticresource.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @ClassName WebMvcConfig
 * @Description Web配置类
 * @Author panchao
 * @Date 2019/6/8 18:08
 * @Version 1.0
 */
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/custom/**")
                .addResourceLocations("file:D:/custom/");
    }
}
```
上面配置类的意思就是：将所有/custom/**访问都映射到D:/custom/路径下

### 第二种方式：

在SpringBoot配置文件application.yaml中进行配置；

配置如下：
```yaml
static:
  upload-path: D:/
spring:
  mvc:
    static-path-pattern: /**
  resources:
    static-locations: classpath:/META-INF/resources/,classpath:/resources/,classpath:/static/,classpath:/public/,file:${static.upload-path}
```
**注意：**

- `static.upload-path`这个属于自定义的属性，指定了一个路径，注意要以/结尾；
- `spring.mvc.static-path-pattern=/**`表示所有的访问都经过静态资源路径；
- `spring.resources.static-locations`在这里配置静态资源路径，前面说了这里的配置是覆盖默认配置，所以需要将默认的也加上否则static、public等这些路径将不能被当作静态资源路径，在这个最末尾的file:${web.upload-path}之所以要加file:是因为指定的是一个具体的硬盘路径，其他的使用classpath指的是系统环境变量

使用SpringBoot框架开发项目，趋向于：

1. 动静分离---后台服务与前台页面,图片分开部署
2. 静态资源使用CDN加速增强访问速度

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
