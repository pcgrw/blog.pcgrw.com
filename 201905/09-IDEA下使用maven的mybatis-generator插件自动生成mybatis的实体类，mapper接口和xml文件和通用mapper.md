#### 1.在pom.xml文件中配置mybatis-generator-maven-plugin插件：
```xml
<!-- 自动生成mybatis代码 -->
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.3.5</version>
    <configuration>
        <configurationFile>src/main/resources/generator/generator.xml</configurationFile>
        <overwrite>true</overwrite>
    </configuration>
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.34</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.3.5</version>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper</artifactId>
            <version>3.4.0</version>
        </dependency>
    </dependencies>
</plugin>
```
#### 2.在项目resources下创建generator文件夹，添加配置文件generatorConfig.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <properties resource="jdbc.properties"></properties>
    <!-- 这里记录了数据库连接 -->
    <context defaultModelType="flat" id="mysql" targetRuntime="MyBatis3">
        <!-- 生成的Java文件的编码 -->
        <property name="javaFileEncoding" value="UTF-8"/>
        <!-- 生成根Mapper，用户可以看后边代码 -->
        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <property name="mappers" value="tk.mybatis.mapper.common.BaseMapper"/>
        </plugin>

        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="${jdbc.url}"
                        userId="${jdbc.username}" password="${jdbc.password}">
        </jdbcConnection>

        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
           NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="true" />
        </javaTypeResolver>

        <javaModelGenerator targetPackage="com.pcgrw.weblog.model.entity"
                            targetProject="src/main/java">
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <sqlMapGenerator targetPackage="mapper"
                         targetProject="src/main/resources">
        </sqlMapGenerator>

        <!--
            1，ANNOTATEDMAPPER：会生成使用Mapper接口+Annotation的方式创建（SQL生成在annotation中），不会生成对应的XML；
            2，XMLMAPPER：会生成Mapper接口，接口完全依赖XML；
        -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.pcgrw.weblog.mapper"
                             targetProject="src/main/java">
        </javaClientGenerator>

        <table tableName="weblog_attachment" domainObjectName="Attachment" selectByExampleQueryId="false" enableSelectByExample="false" enableDeleteByExample="false" enableUpdateByExample="false" enableCountByExample="false" ></table>
    </context>
</generatorConfiguration>
```
**注意：** 在配置文件中数据库连接我引用的jdbc.properties文件中的配置信息，你也可以自己手动写死。

#### 3.在maven命令框中执行命令`mybatis-generator:generate -e`或者在maven插件界面中点击mybatis-generator:generate按钮，就可以直接生成mybatis相关文件。
