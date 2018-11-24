---

title: SpringBoot笔记系列：（二十）多环境配置及切换

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: ceff6304

date: 2018-07-22 21:09:40

---

一个项目中，开发环境、测试环境和生产环境中的配置文件是不相同的，如果只有一套配置文件，在切换环境是很不方便，因此我们可以配置多个环境配置文件，然后再主配置文件中进行切换即可。

<!-- more -->

1. 新增开发、测试和生产配置文件：
	1. application-dev.properties

	```properties
	server.port=8080
	```

	2. application-uat.properties

	```properties
	server.port=8081
	```

	3. application-prd.properties

	```properties
	server.port=8082
	```

2. 在主配置文件application.properties中选择使用哪一个配置文件

    ```properties
    #选用开发配置文件application-dev.properties
    spring.profiles.active=dev
    ```

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
