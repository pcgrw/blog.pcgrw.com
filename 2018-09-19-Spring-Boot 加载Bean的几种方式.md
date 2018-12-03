---

title: Spring-Boot 加载Bean的几种方式

categories:

- SpringBoot

tags:

- SpringBoot 

abbrlink: af8df786

date: 2018-09-19 20:41:31

---

Spring从3.0之后，就逐步倾向于使用java code config方式来进行bean的配置，在spring-boot中，这种风格就更为明显了。

在查看spring-boot工程的时候，总想着探究一下spring-boot如何简单的声明一个starter、Enable\*\*，就能额外增加一个强大的功能，spring是如何找到这些具体的实现bean的呢。

目前，大概有这么几种：

1. 直接在工程中使用@Configuration注解

	这个就是基本的java code方式，在@SpringBootApplication里面就包含了@ComponentScan，因而会把工程中的@Configuration注解找到，并加以解释。

2. 通过@Enable××注解里面的@Import注解

	我们在Enable某个功能时，实际上是通过@Import注解加载了另外的配置属性类。

	例如: 如果要给工程加上定时任务的功能，只需要在某个配置文件上加上@EnableScheduling，实际上它是引入了SchedulingConfiguration.class，代码如下：

	```java
	@Target(ElementType.TYPE)
	@Retention(RetentionPolicy.RUNTIME)
	@Import(SchedulingConfiguration.class)
	@Documented
	public @interface EnableScheduling {
	}
	```

	而SchedulingConfiguration就是一个标准的配置文件了，里面定义了ScheduledAnnotationBeanPostProcessor这个bean。

	```java
	@Configuration
	@Role(BeanDefinition.ROLE_INFRASTRUCTURE)
	public class SchedulingConfiguration {
	    @Bean(name = TaskManagementConfigUtils.SCHEDULED_ANNOTATION_PROCESSOR_BEAN_NAME)
	    @Role(BeanDefinition.ROLE_INFRASTRUCTURE)
	    public ScheduledAnnotationBeanPostProcessor scheduledAnnotationProcessor() {
		return new ScheduledAnnotationBeanPostProcessor();
	    }
	}
	```

	有了ScheduledAnnotationBeanPostProcessor这bean，就会在context初始化时候，查找我们代码中的@Scheduled，并把它们转换为定时任务。

3. 通过@EnableAutoConfiguration注解

	添加了这个异常强大的注解，spring-boot会利用AutoConfigurationImportSelector搜索所有jar包中META-INF文件夹中spring.factories，找到其中org.springframework.boot.autoconfigure.EnableAutoConfiguration的属性值，并把它作为需要解析的@Configuration文件。

	例如：spring-cloud-commons里面的spring.factories

	```
	# AutoConfiguration
	org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
	org.springframework.cloud.client.CommonsClientAutoConfiguration,\
	org.springframework.cloud.client.discovery.noop.NoopDiscoveryClientAutoConfiguration,\
	org.springframework.cloud.client.hypermedia.CloudHypermediaAutoConfiguration,\
	org.springframework.cloud.client.loadbalancer.AsyncLoadBalancerAutoConfiguration,\
	org.springframework.cloud.client.loadbalancer.LoadBalancerAutoConfiguration,\
	org.springframework.cloud.client.serviceregistry.ServiceRegistryAutoConfiguration,\
	org.springframework.cloud.commons.util.UtilAutoConfiguration,\
	org.springframework.cloud.client.discovery.simple.SimpleDiscoveryClientAutoConfiguration
	```

4. 自己实现ImportSelector

	AutoConfigurationImportSelector显然有时候还是不够用的，这时候就可以自己实现ImportSelector，实现更灵活的加载功能。

	```java
	public interface ImportSelector {
	    String[] selectImports(AnnotationMetadata importingClassMetadata);
	}
	```

	ImportSelector接口定义了一个selectImports()方法,根据程序里面定义的注解信息，动态返回能被加载的类列表。这个实现就非常灵活了，简单的可以自己判断注解，直接返回类名；复杂的可以自己定制类似AutoConfigurationImportSelector的功能。

	例如：我们看看spring-cloud的@EnableCircuitBreaker

	```java
	@Target(ElementType.TYPE)
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	@Inherited
	@Import(EnableCircuitBreakerImportSelector.class)
	public @interface EnableCircuitBreaker {
	}
	```

	发现，它引入了EnableCircuitBreakerImportSelector，它本身并没有实现ImportSelector，而是其父类SpringFactoryImportSelector实现的。

	```java
	@Override
	public String[] selectImports(AnnotationMetadata metadata) {
	if (!isEnabled()) {
	    return new String[0];
	}
	AnnotationAttributes attributes = AnnotationAttributes.fromMap(
		metadata.getAnnotationAttributes(this.annotationClass.getName(), true));
	Assert.notNull(attributes, "No " + getSimpleName() + " attributes found. Is "
		+ metadata.getClassName() + " annotated with @" + getSimpleName() + "?");
	// Find all possible auto configuration classes, filtering duplicates
	// 调用SpringFactoriesLoader的loadFactoryNames去加载
	List<String> factories = new ArrayList<>(new LinkedHashSet<>(SpringFactoriesLoader
		.loadFactoryNames(this.annotationClass, this.beanClassLoader)));
	//省略了错误判断和多于一个的log
	return factories.toArray(new String[factories.size()]);
	}
	```

	这里，我们看到，实际加载的代码是传入了this.annotationClass，那么对于EnableCircuitBreakerImportSelector来说，就是在spring.factories找它的全类名：
org.springframework.cloud.client.circuitbreaker.EnableCircuitBreakerImportSelector对应的值。

	最终在spring-cloud-netflix-core-××.jar的pring.factories中找到如下配置：

	```
	org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker=\
	org.springframework.cloud.netflix.hystrix.HystrixCircuitBreakerConfiguration
	```

	这样就完成了通过@EnableCircuitBreaker的注解，最终加载到Hystrix的实现ystrixCircuitBreakerConfiguration，实现了功能定义和具体实现的分离。
