---

title: SpringBoot笔记系列：（二十一）修改端口号及项目访问路径

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: 1a3dda77

date: 2018-07-22 21:23:55

---

springboot中修改端口号及项目访问路径：

<!-- more -->

在application.properties配置文件中添加配置信息

```properties
#端口号
server.port=8081
#访问路径
server.servlet.context-path=/springboot-helloworld
```

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
