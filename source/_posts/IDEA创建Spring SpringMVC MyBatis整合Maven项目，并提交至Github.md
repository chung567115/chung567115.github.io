---
title: IDEA创建Spring SpringMVC MyBatis整合Maven项目，并提交至Github
categories: 
  - Java
tags:
  - Java
  - SpringMVC
  - MyBatis
date: 2017-12-10 15:34:30
---

> 本文小博将写一篇IDEA创建SSM+Maven整合项目，并提交至Github的教程

<!-- more -->

# IDEA创建Spring SpringMVC MyBatis整合Maven项目，并提交至Github

①新建Maven项目，如下图顺序。第三张图片中，添加一个参数archetypeCatalog=internal，表示仅使用内部元数据，不用这个参数创建Maven项目会很慢哦。其他起名字什么的大家大家随意，就不一一解释了。
![1](/4-1.png)
![2](/4-2.png)
![3](/4-3.png)
![4](/4-4.png)

②将Java文件夹标记为源码目录，然后创建各种MVC模式对应的包
![5](/4-5.png)

③如下图，创建配置文件，然后进行各种配置文件的编写。后面贴出我的代码，篇幅太大就不逐一解释了，以后有时间再详细拉出来写写。
![7](/4-6.png)

- pom.xml文件
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>xin.chung</groupId>
    <artifactId>undergraduate</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>undergraduate</name>
    <url>http://maven.apache.org</url>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <!-- JDK版本号 -->
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <!-- spring版本号 -->
        <spring.version>4.2.5.RELEASE</spring.version>
        <!-- mybatis版本号 -->
        <mybatis.version>3.2.8</mybatis.version>
        <!-- mysql驱动版本号 -->
        <mysql-driver.version>5.1.29</mysql-driver.version>
        <!-- log4j日志包版本号 -->
        <slf4j.version>1.7.18</slf4j.version>
        <log4j.version>1.2.17</log4j.version>
    </properties>
    <dependencies>
        <!-- 添加jstl依赖 -->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <dependency>
            <groupId>javax</groupId>
            <artifactId>javaee-api</artifactId>
            <version>7.0</version>
        </dependency>
        <!-- 添加junit4依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <!-- 指定范围，在测试时才会加载 -->
            <scope>test</scope>
        </dependency>
        <!-- 添加spring核心依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-oxm</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!-- 添加mybatis依赖 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis.version}</version>
        </dependency>
        <!-- 添加mybatis/spring整合包依赖 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.2.2</version>
        </dependency>
        <!-- 添加mysql驱动依赖 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql-driver.version}</version>
        </dependency>
        <!-- 添加数据库连接池依赖 -->
        <dependency>
            <groupId>commons-dbcp</groupId>
            <artifactId>commons-dbcp</artifactId>
            <version>1.2.2</version>
        </dependency>
        <!-- 添加fastjson -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.1.41</version>
        </dependency>
        <!-- 添加日志相关jar包 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <!-- log end -->
        <!-- 映入JSON -->
        <dependency>
            <groupId>org.codehaus.jackson</groupId>
            <artifactId>jackson-mapper-asl</artifactId>
            <version>1.9.13</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.8.0</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.8.0</version>
        </dependency>
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>
        <dependency>
            <groupId>commons-codec</groupId>
            <artifactId>commons-codec</artifactId>
            <version>1.9</version>
        </dependency>
    </dependencies>
    <build>
        <finalName>undergraduate</finalName>
    </build>
</project>
```
- web.xml文件
```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
              http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">
    <display-name>undergraduate</display-name>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-mybatis.xml</param-value>
    </context-param>

    <context-param>
        <param-name>log4jConfigLocation</param-name>
        <param-value>classpath:log4j.properties</param-value>
    </context-param>

    <!-- 编码过滤器 -->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!-- spring监听器 -->
    <listener>
        <listener-class>
	        org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>

    <!-- 防止spring内存溢出监听器，比如quartz -->
    <listener>
        <listener-class>
	        org.springframework.web.util.IntrospectorCleanupListener
        </listener-class>
    </listener>

    <!-- spring mvc servlet-->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>
	        org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
        <async-supported>true</async-supported>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <!-- 此处也可以配置成 *.do 形式 -->
        <url-pattern>*.action</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>login.jsp</welcome-file>
    </welcome-file-list>

    <!-- session配置 -->
    <session-config>
        <session-timeout>20</session-timeout>
    </session-config>
</web-app>
```
- jdbc.properties文件
```properties
driverClass=com.mysql.jdbc.Driver
#以下三行根据自己的数据库来设置
jdbcUrl=jdbc:mysql://localhost:3306/graduate?useUnicode=true&characterEncoding=utf-8
username=root
password=root
#定义初始连接数
initialSize=0
#定义最大连接数
maxActive=20
#定义最大空闲
maxIdle=20
#定义最小空闲
minIdle=1
#定义最长等待时间
maxWait=3600
```
- log4j.properties文件，这个根据自己个人情况来哦
```properties
log4j.rootLogger=INFO,Console,File
#控制台日志
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.Target=System.out
log4j.appender.Console.layout=org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=
[%p][%t][%d{yyyy-MM-dd HH\:mm\:ss}][%C] - %m%n
#普通文件日志
log4j.appender.File=org.apache.log4j.RollingFileAppender
log4j.appender.File.File=logs/ssm.log
log4j.appender.File.MaxFileSize=10MB
#输出日志，如果换成DEBUG表示输出DEBUG以上级别日志
log4j.appender.File.Threshold=ALL
log4j.appender.File.layout=org.apache.log4j.PatternLayout
log4j.appender.File.layout.ConversionPattern=
[%p][%t][%d{yyyy-MM-dd HH\:mm\:ss}][%C] - %m%n
#设置输出日志文件编码
log4j.appender.File.Encoding=UTF-8
```
- spring-mvc.xml文件，注意自己建立的包和我教程里包的不同
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:context="http://www.springframework.org/schema/context"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-4.0.xsd">

    <!-- 自动扫描  @Controller-->
    <context:component-scan base-package="xin.chung.undergraduate.controller"/>

    <!--避免IE执行AJAX时，返回JSON出现下载文件 -->
    <bean id="mappingJacksonHttpMessageConverter" 
.MappingJackson2HttpMessageConverter">
        <property name="supportedMediaTypes">
            <list>
                <value>application/json;charset=UTF-8</value>
            </list>
        </property>
    </bean>
    <!-- 启动SpringMVC的注解功能，完成请求和注解POJO的映射 -->
    <bean class="org.springframework.web.servlet.mvc.annotation
    .AnnotationMethodHandlerAdapter">
        <property name="messageConverters">
            <list>
                <!-- JSON转换器 -->
                <ref bean="mappingJacksonHttpMessageConverter"/>
            </list>
        </property>
    </bean>

    <!--定义跳转的文件的前后缀 ，视图模式配置-->
    <bean class="org.springframework.web.servlet.view
    .InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/view/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```
- spring-mybatis.xml文件，同样注意自己建立的包和我教程里包的不同
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:context="http://www.springframework.org/schema/context" 
xmlns:tx="http://www.springframework.org/schema/tx"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-3.1.xsd
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- 自动扫描 -->
    <context:component-scan base-package="xin.chung.undergraduate"/>

    <!-- 第一种方式：加载一个properties文件 -->
    <bean id="propertyConfigurer"
 class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="location" value="classpath:jdbc.properties"/>
    </bean>
    <!-- 第二种方式：加载多个properties文件
    <bean id="configProperties"     
    class="org.springframework.beans.factory.config.PropertiesFactoryBean">
        <property name="locations">
            <list>
                <value>classpath:jdbc.properties</value>
                <value>classpath:common.properties</value>
            </list>
        </property>
        <property name="fileEncoding" value="UTF-8"/>
    </bean>
    <bean id="propertyConfigurer" 
    class="org.springframework.beans.factory.config.PreferencesPlaceholderConfigurer">
        <property name="properties" ref="configProperties"/>
    </bean>
    -->

    <!-- 配置数据源 -->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
          destroy-method="close">
        <property name="driverClassName" value="${driverClass}"/>
        <property name="url" value="${jdbcUrl}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
        <!-- 初始化连接大小 -->
        <property name="initialSize" value="${initialSize}"></property>
        <!-- 连接池最大数量 -->
        <property name="maxActive" value="${maxActive}"></property>
        <!-- 连接池最大空闲 -->
        <property name="maxIdle" value="${maxIdle}"></property>
        <!-- 连接池最小空闲 -->
        <property name="minIdle" value="${minIdle}"></property>
        <!-- 获取连接最大等待时间 -->
        <property name="maxWait" value="${maxWait}"></property>
    </bean>

    <!-- mybatis和spring完美整合，不需要mybatis的配置映射文件 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!-- 自动扫描mapping.xml文件 -->
        <property name="mapperLocations" value="classpath:mapper/*.xml"></property>
    </bean>

    <!-- DAO接口所在包名，Spring会自动查找其下的类 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="xin.chung.undergraduate.dao"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
    </bean>

    <!-- (事务管理)transactionManager, use JtaTransactionManager for global tx -->
    <bean id="transactionManager" 
 class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```

④发布到Tomcat，开启服务器并运行调试，步骤如下图，其中各种命名具体情况具体对待。
![8](/4-7.png)
![9](/4-8.png)
![10](/4-9.png)

⑤编写下列文件，并开启Tomcat测试运行项目。

- resources\mapper\TestMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="xin.chung.undergraduate.dao.ITestDao">
    <resultMap id="TestCMap" type="xin.chung.undergraduate.model.po.TestC">
        <id column="id" property="id" jdbcType="BIGINT"/>
        <result column="username" property="username" jdbcType="VARCHAR"/>
        <result column="password" property="password" jdbcType="VARCHAR"/>
        <result column="phone" property="phone" jdbcType="VARCHAR"/>
        <result column="email" property="email" jdbcType="VARCHAR"/>
        <result column="create_time" property="createTime" jdbcType="DATE"/>
    </resultMap>
    <!-- <insert id="CreateUser" parameterType="java.util.Map">
         INSERT INTO t_testc
     </insert>-->
    <select id="ReadUserByID" parameterType="java.lang.Long" resultMap="TestCMap">
        SELECT * FROM t_testc
        WHERE id = #{userID}
    </select>
</mapper>
```
- java\xin\chung\undergraduate\dao\ITestDao.java
```java
/**
 * Created by Charle Chung on 2017/10/13.
 */
@Repository
public interface ITestDao {
    boolean CreateUser(TestC testC);

    boolean UpdateUser(TestC testC);

    TestC ReadUserByID(@Param("userID") Long userID);

    boolean DeleteUserByID(@Param("userID") Long userID);
}
```
- java\xin\chung\undergraduate\service\ITestService.java
```java
/**
 * Created by Charle Chung on 2017/10/13.
 */
public interface ITestService {
    boolean CreateUser();

    boolean UpdateUser();

    TestC ReadUserByID(Long userID);

    boolean DeleteUserByID(Long userID);
}
```
- java\xin\chung\undergraduate\service\impl\TestService.java
```java
/**
 * Created by Charle Chung on 2017/10/13.
 */
@Service
@Transactional(rollbackFor = Exception.class)
public class TestService implements ITestService {
    @Resource
    private ITestDao iTestDao;
    @Override
    public boolean CreateUser() {
        return false;
    }
    @Override
    public boolean UpdateUser() {
        return false;
    }
    @Override
    public TestC ReadUserByID(Long userID) {
        return iTestDao.ReadUserByID(userID);
    }
    @Override
    public boolean DeleteUserByID(Long userID) {
        return false;
    }
}
```
- java\xin\chung\undergraduate\controller\TestController.java
```java
/**
 * Created by Charle Chung on 2017/10/13.
 */
@Controller
@RequestMapping(value = "/test")
public class TestController {
    @Resource
    private ITestService iTestService;
    @ResponseBody
    @RequestMapping(value = "/test.action", method = RequestMethod.GET)
    public TestC testC(TestDTO testDTO) {
        TestC testC = iTestService.ReadUserByID(testDTO.getUid());
        return testC;
    }
    
//    Map<String, Object> data = new HashMap<String, Object>();
//
//    public void setData(String key, Object value) {
//        data.clear();
//        data.put(key, value);
//    }

//    @RequestMapping(value = "/test.action", method = RequestMethod.POST)
//    public ModelAndView testC(TestDTO testDTO) {
//        System.out.println("test");
//        TestC testC = iTestService.ReadUserByID(testDTO.getUid());
//        if (testC != null) {
//            setData("json", JSONArray.toJSON(testC));
//        } else {
//            setData("json", "null");
//        }
//        return new ModelAndView("result", data);
//    }
}
```
- graduate数据库表t_testc结构及数据如下图
![16](/4-10.png)

⑥处理好以上内容后，开启Tomcat，浏览器访问http://localhost:8080/graduate/test/test.action?uid=1，如若项目配置正确，应出现如下JSON。
![15](/4-11.png)

⑦分享项目到Github
![11](/4-12.png)
![12](/4-13.png)
![13](/4-14.png)
![14](/4-15.png)

----------
