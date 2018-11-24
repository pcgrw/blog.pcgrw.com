---

title: SpringBoot笔记系列：（二十二）使用yml配置文件

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: 2e4f3861

date: 2018-07-22 21:45:58

---

> SpringBoot推按使用yml配置文件来代替properties配置文件

<!-- more -->

新增application.yml配置文件

```yml
server:
  port: 8080
  servlet:
    context-path: /springboot-helloworld
```

删除application.properties配置文件

```properties
server.port=8080
server.servlet.context-path=/springboot-helloworld
```

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
