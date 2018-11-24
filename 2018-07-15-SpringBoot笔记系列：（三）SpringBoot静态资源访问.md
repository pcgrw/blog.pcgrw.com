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

在我们开发Web应用的时候，需要引用大量的js、css、图片等静态资源。

<!-- more -->

Spring Boot默认提供静态资源目录位置需置于classpath下，目录名需符合如下规则：

- resources/static
- resources/public
- resources/resources
- resources/META-INF/resources

> 举例：我们可以在src/main/resources/目录下创建static，在该位置放置一个图片文件。启动程序后，尝试访问 http://localhost:8080/tupian.jpg ，如能显示图片，配置成功。

使用SpringBoot框架开发项目，趋向于：

1. 动静分离---后台服务与前台页面,图片分开部署
2. 静态资源使用CDN加速增强访问速度

[SpringBoot笔记系列目录](e0c584e.html)
