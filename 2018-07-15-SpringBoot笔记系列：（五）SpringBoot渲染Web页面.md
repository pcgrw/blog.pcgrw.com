---

title: SpringBoot笔记系列：（五）SpringBoot渲染Web页面

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: da44bc20

date: 2018-07-15 11:23:08

---

在之前的示例中，我们都是通过@RestController来处理请求，所以返回的内容为json对象。那么如果需要渲染html页面的时候，要如何实现呢？

<!-- more -->

模板引擎:在动态HTML实现上Spring Boot依然可以完美胜任，并且提供了多种模板引擎的默认配置支持，所以在推荐的模板引擎下，我们可以很快的上手开发动态网站。

Spring Boot提供了默认配置的模板引擎主要有以下几种：

1. Thymeleaf
2. FreeMarker
3. Velocity
4. Groovy
5. Mustache

Spring Boot建议使用这些模板引擎，避免使用JSP，若一定要使用JSP将无法实现Spring Boot的多种特性，具体可见后文：支持JSP的配置.

当使用上述模板引擎中的任何一个，它们默认的模板配置路径为：src/main/resources/templates。当然也可以修改这个路径，具体如何修改，可在后续各模板引擎的配置属性中查询并修改。

### 使用Freemarker模板引擎渲染web视图步骤： ###

#### 1. pom文件引入 ####

```xml
<!-- 引入freeMarker的依赖包. -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

#### 2. application.properties中Freemarker配置 ####

```properties
########################################################
###FREEMARKER (FreeMarkerAutoConfiguration)
########################################################
spring.freemarker.allow-request-override=false
spring.freemarker.cache=true
spring.freemarker.check-template-location=true
spring.freemarker.charset=UTF-8
spring.freemarker.content-type=text/html
spring.freemarker.expose-request-attributes=false
spring.freemarker.expose-session-attributes=false
spring.freemarker.expose-spring-macro-helpers=false
#spring.freemarker.prefix=
#spring.freemarker.request-context-attribute=
#spring.freemarker.settings.*=
spring.freemarker.suffix=.html
#模板加载路径 按需配置
spring.freemarker.template-loader-path=classpath:/templates/
#comma-separated list
#spring.freemarker.view-names= # whitelist of view names that can be resolved
```

#### 3. 后台代码 ####

```java
package top.pcstar.springboothelloworld.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.ModelAndView;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @Author: PanChao
 * @Description: Freemarker测试
 * @Date: Created in 11:38 2018/7/15
 */
@RestController
public class FreemarkerController {
    @RequestMapping("/testFreemarker")
    public ModelAndView testFreemarker() {
        Map<String, Object> result = new HashMap<>();
        result.put("name","美丽的天使...");
        result.put("sex", "0");
        List<String> listResult = new ArrayList<>();
        listResult.add("zhangsan");
        listResult.add("lisi");
        listResult.add("wangwu");
        result.put("listResult", listResult);
        return new ModelAndView("index","result",result);
    }
}
```

#### 4. 前台代码(在resources\templates路径下) ####

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
${result.name}
<#if result.sex=="1">
男
<#elseif result.sex=="2">
女
<#else>
其他
</#if>
<#list result.listResult as user>
${user}
</#list>
</body>
</html>
```

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
