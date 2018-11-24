---

title: SpringBoot笔记系列：（二十四）集成EhCache缓存

categories:

- Java
- SpringBoot学习笔记

tags:

- SpringBoot

abbrlink: d1213e26

date: 2018-07-22 22:27:05

---

> SpringBoot集成EhCache缓存以及EhCache缓存的使用

<!-- more -->

1. pom文件添加缓存jar包

	```xml
	<!-- EhCache缓存jar包 -->
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter-cache</artifactId>
	</dependency>
	```

2. 新建ehcache.xml文件

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
	         updateCheck="false">
	    <diskStore path="java.io.tmpdir/Tmp_EhCache" />
	
	    <!-- 默认配置 -->
	    <defaultCache maxElementsInMemory="5000" eternal="false"
	                  timeToIdleSeconds="120" timeToLiveSeconds="120"
	                  memoryStoreEvictionPolicy="LRU" overflowToDisk="false" />
	
	    <cache name="baseCache" maxElementsInMemory="10000"
	           maxElementsOnDisk="100000" />
	
	</ehcache>
	```

3. 代码使用@CacheConfig和@Cacheable注解

	```java
	package top.pcstar.springbootmybatis.mapper;
	
	import org.apache.ibatis.annotations.Insert;
	import org.apache.ibatis.annotations.Param;
	import org.apache.ibatis.annotations.Select;
	import org.springframework.cache.annotation.CacheConfig;
	import org.springframework.cache.annotation.Cacheable;
	import org.springframework.stereotype.Repository;
	import top.pcstar.springbootmybatis.entity.Test;
	
	/**
	 * @Author: PanChao
	 * @Description:
	 * @Date: Created in 16:31 2018/7/17
	 */
	@Repository
	@CacheConfig(cacheNames = "baseCache")
	public interface TestMapper {
	    @Cacheable
	    @Select("SELECT * FROM TEST WHERE NAME = #{name}")
	    Test findByName(@Param("name") String name);
	
	    @Insert("INSERT INTO TEST(NAME, SEX) VALUES(#{name}, #{sex})")
	    int insert(@Param("name") String name, @Param("sex") String sex);
	}
	```

4. 清除缓存

	```java
	@Autowired
	private CacheManager cacheManager;
	@RequestMapping("/remoKey")
	public void remoKey() {
	    cacheManager.getCache("baseCache").clear();
	}
	```

5. 使用缓存

	```java
	@Autowired
	private CacheManager cacheManager;
	@RequestMapping("/getKey")
	public Test getKey() {
	    Cache cache = cacheManager.getCache("baseCache");
	    return (Test) cache.get("123").get();
	}
	```

6. 启动加入缓存(使用@EnableCaching注解开启缓存)

	```java
	package top.pcstar.springbootmybatis;
	
	import org.mybatis.spring.annotation.MapperScan;
	import org.springframework.boot.SpringApplication;
	import org.springframework.boot.autoconfigure.SpringBootApplication;
	import org.springframework.cache.annotation.EnableCaching;
	
	@MapperScan("top.pcstar.springbootmybatis.mapper")
	@SpringBootApplication
	    @EnableCaching // 开启缓存注解
	public class SpringbootMybatisApplication {
	
	    public static void main(String[] args) {
	        SpringApplication.run(SpringbootMybatisApplication.class, args);
	    }
	}
	```

[SpringBoot笔记系列目录](./2018-05-28-SpringBoot笔记系列目录.md)
