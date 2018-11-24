---

title: Spring中@Import注解的作用和使用

categories:

- Java
- Spring

tags:

- Spring

abbrlink: 87ecf74

date: 2018-09-28 11:51:32

---

> @Import用来导入@Configuration注解的配置类、声明@Bean注解的bean方法、导入ImportSelector的实现类或导入ImportBeanDefinitionRegistrar的实现类。

<!-- more -->

#### @Import注解的作用 ####

查看Import注解源码

```java
/**
 * Indicates one or more {@link Configuration @Configuration} classes to import.
 *
 * <p>Provides functionality equivalent to the {@code <import/>} element in Spring XML.
 * Only supported for classes annotated with {@code @Configuration} or declaring at least
 * one {@link Bean @Bean} method, as well as {@link ImportSelector} and
 * {@link ImportBeanDefinitionRegistrar} implementations.
 *
 * <p>{@code @Bean} definitions declared in imported {@code @Configuration} classes
 * should be accessed by using {@link org.springframework.beans.factory.annotation.Autowired @Autowired}
 * injection. Either the bean itself can be autowired, or the configuration class instance
 * declaring the bean can be autowired. The latter approach allows for explicit,
 * IDE-friendly navigation between {@code @Configuration} class methods.
 *
 * <p>May be declared at the class level or as a meta-annotation.
 *
 * <p>If XML or other non-{@code @Configuration} bean definition resources need to be
 * imported, use {@link ImportResource @ImportResource}
 *
 * @author Chris Beams
 * @since 3.0
 * @see Configuration
 * @see ImportSelector
 * @see ImportResource
 */
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Import {

	/**
	 * The @{@link Configuration}, {@link ImportSelector} and/or
	 * {@link ImportBeanDefinitionRegistrar} classes to import.
	 */
	Class<?>[] value();
}
```

分析类注释得出结论：

1. 声明一个bean
2. 导入@Configuration注解的配置类
3. 导入ImportSelector的实现类
4. 导入ImportBeanDefinitionRegistrar的实现类

#### @Import注解的使用 ####

1. 声明一个bean

	```java
	package com.example.demo.bean;
	
	public class TestBean1 {
	}
	
	package com.example.demo;
	
	import com.example.demo.bean.TestBean1;
	import org.springframework.context.annotation.Configuration;
	import org.springframework.context.annotation.Import;
	
	@Import({TestBean1.class})
	@Configuration
	public class AppConfig {
	}
	```

2. 导入@Configuration注解的配置类

	```java
	package com.example.demo.bean;

	public class TestBean2 {
	}

	package com.example.demo.bean;
	
	import org.springframework.context.annotation.Bean;
	import org.springframework.context.annotation.Configuration;
	
	@Configuration
	public class TestConfig {
	    @Bean
	    public TestBean2 getTestBean2(){
	        return new TestBean2();
	    }
	}
	
	package com.example.demo;
	
	import com.example.demo.bean.TestBean1;
	import com.example.demo.bean.TestConfig;
	import org.springframework.context.annotation.Configuration;
	import org.springframework.context.annotation.Import;
	
	@Import({TestBean1.class,TestConfig.class})
	@Configuration
	public class AppConfig {
	}
	```

3. 导入ImportSelector的实现类

	```java
	package com.example.demo.bean;

	public class TestBean3 {
	}

	package com.example.demo.bean;
	
	import org.springframework.context.annotation.ImportSelector;
	import org.springframework.core.type.AnnotationMetadata;
	
	public class TestImportSelector implements ImportSelector {
	    @Override
	    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
	        return new String[]{"com.example.demo.bean.TestBean3"};
	    }
	}
	
	package com.example.demo;
	
	import com.example.demo.bean.TestBean1;
	import com.example.demo.bean.TestConfig;
	import com.example.demo.bean.TestImportSelector;
	import org.springframework.context.annotation.Configuration;
	import org.springframework.context.annotation.Import;
	
	@Import({TestBean1.class,TestConfig.class,TestImportSelector.class})
	@Configuration
	public class AppConfig {
	}
	```

4. 导入ImportBeanDefinitionRegistrar的实现类

	```java
	package com.example.demo.bean;

	public class TestBean4 {
	}

	package com.example.demo.bean;
	
	import org.springframework.beans.factory.support.BeanDefinitionRegistry;
	import org.springframework.beans.factory.support.RootBeanDefinition;
	import org.springframework.context.annotation.ImportBeanDefinitionRegistrar;
	import org.springframework.core.type.AnnotationMetadata;
	
	public class TestImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
	    @Override
	    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
	        RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(TestBean4.class);
	        registry.registerBeanDefinition("TestBean4", rootBeanDefinition);
	    }
	}
	
	package com.example.demo;
	
	import com.example.demo.bean.TestBean1;
	import com.example.demo.bean.TestConfig;
	import com.example.demo.bean.TestImportBeanDefinitionRegistrar;
	import com.example.demo.bean.TestImportSelector;
	import org.springframework.context.annotation.Configuration;
	import org.springframework.context.annotation.Import;
	
	@Import({TestBean1.class,TestConfig.class,TestImportSelector.class,TestImportBeanDefinitionRegistrar.class})
	@Configuration
	public class AppConfig {
	}
	```

最后，我们来看下导入结果：

```java
package com.example.demo;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.Arrays;

@RunWith(SpringRunner.class)
@SpringBootTest
public class DemoApplicationTests {

    @Test
    public void test() {
        AnnotationConfigApplicationContext annotationConfigApplicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        String[] beanDefinitionNames = annotationConfigApplicationContext.getBeanDefinitionNames();
        System.out.println("--------------------------------------------------------");
        for (String beanDefinitionName: beanDefinitionNames) {
            System.out.println(beanDefinitionName);
        }
        System.out.println("--------------------------------------------------------");
    }

}
```

打印结果如下：

```
    --------------------------------------------------------
    org.springframework.context.annotation.internalConfigurationAnnotationProcessor
    org.springframework.context.annotation.internalAutowiredAnnotationProcessor
    org.springframework.context.annotation.internalRequiredAnnotationProcessor
    org.springframework.context.annotation.internalCommonAnnotationProcessor
    org.springframework.context.event.internalEventListenerProcessor
    org.springframework.context.event.internalEventListenerFactory
    appConfig
    com.example.demo.bean.TestBean1
    com.example.demo.bean.TestConfig
    getTestBean2
    com.example.demo.bean.TestBean3
    TestBean4
    --------------------------------------------------------
```

可以看出TestBean1,TestBean2,TestBean3,TestBean4通过不同的4种导入方法被导入SpringIOC容器中。