---

title: SpringBoot笔记系列：（四）SpringBoot全局捕获异常

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: 5f8b10e9

date: 2018-07-15 11:12:35

---

什么是全局捕获异常?  
在进行web开发时，我们不能把异常信息直接返回给客户查看，例如404，500等错误，用户并不知道这些是什么错误，应该使用全局捕获异常对异常信息进行捕获处理，返回用户可以看懂的错误信息。

<!-- more -->

@ControllerAdvice：

1. 是 controller 的一个辅助类，最常用的就是作为全局异常处理的切面类
2. 可以指定扫描范围
3. 约定了几种可行的返回值，如果是直接返回 model 类的话，需要使用 @ResponseBody 进行 json 转换
	1. 返回 String，表示跳到某个 view
	2. 返回 modelAndView
	3. 返回 model + @ResponseBody

@RestControllerAdvice：

1. @RestControllerAdvice = @ControllerAdvice + @ResponseBody

```java
package top.pcstar.springboothelloworld.exception;

import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.util.HashMap;
import java.util.Map;

/**
 * @Author: PanChao
 * @Description: 全局捕获异常
 * @Date: Created in 10:31 2018/7/15
 */
//@ControllerAdvice
@RestControllerAdvice
public class GlobalExceptionHandle {
    @ExceptionHandler(RuntimeException.class)
//    @ResponseBody
    public Map<String, Object> resultError() {
	Map<String, Object> result = new HashMap<>();
	result.put("errorCode", "500");
	result.put("errorMsg", "系统错误");
	return result;
    }
}
```

这样在系统中一旦出现RuntimeException异常时，系统将执行全局异常处理，将处理后的数据返回给客户端。

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
