---
title: Spring Boot项目搭建极简教程
categories: 
  - Spring Boot
tags:
  - Java
  - Spring Boot
  - 微服务
date: 2017-12-24 23:45:07
---

> 从今天开始，小博将撰写Spring Boot系列博客

<!-- more -->
# Spring Boot项目搭建极简教程

## Spring Boot项目搭建

使用的开发环境是：
 - IDEA 2017.2
 - JDK 1.8
 - Spring Boot 1.5.9

将涉及到的技术栈有：
- JPA
- MyBatis
- Thymeleaf
- Druid
- Redis
- RabbitMQ
- MongoDB
- Quartz

第一篇主题为项目搭建及简单开发与测试，[点击下载完整代码](http://download.csdn.net/download/u012102104/10172445)

①新建项目
![1](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-1.png)

②选择Spring Initializr
![2](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-2.png)

③填写项目相关信息（本系列博客以Maven为例）
![3](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-3.png)

④选择依赖的核心组件DevTools
![4](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-4.png)

⑤选择依赖的Web组件Web
![5](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-5.png)

⑥填写项目名并创建新项目
![6](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-6.png)

⑦src/main/java为源码文件包，src/mian/resources为配置文件包，src/test/java为测试程序包
![7](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-7.png)

⑧一般来说，Spring Boot项目主要包括如下几个包和文件（图中可见的Java文件和properties文件均自动生成）
com.chung.springbootdemo包下：
- comm包：公共类，工具类等
- controller包：控制器
- entity包：实体类
- repository包：数据库访问
- service：业务
- ServletInitializer.java文件：Servlet容器初始化
- SpringbootdemoApplication.java文件：项目的启动类

resources包下：
- static包：静态资源如JS、CSS、图片等
- templates包：页面模板
- application.properties文件：项目配置信息

最外层的pom.xml文件用于配置项目依赖包及其他配置
![8](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-8.png)

 至此，一个Spring Boot项目搭建好了。下面进项简单的Web开发及测试。




 1 简单Web开发：HelloWorldController内容如下，其中@RestController意为Controller内的方法都以json格式输出，如果为@Controller则代表输出为页面。因此不用再另外配置Jackson。写完HelloWorldController后，运行主程序（本例中为SpringbootdemoApplication.java），打开浏览器访问http://localhost:8080/hello?name=charles，即可看到内容：Hello World! charles。

 注：如果遇到Process finished with exit code 0，可以将pom.xml文件中这段注释
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-tomcat</artifactId>
	<scope>provided</scope>
</dependency>
```

```java
@RestController
public class HelloWorldController {
    @RequestMapping(value = "/hello")
    public String helloWorld(String name) {
        return "Hello World! " + name;
    }
}
```

----------
2 单元测试：在Spring Boot中，可使用MockMvc进行web测试，MockMvc内置了很多工具类和方法，可以模拟get以及post请求，并且打印返回的内容。其中在类上加@SpringBootTest，系统会自动加载SpringBoot容器，在平时我们就可以注入bean来做局部业务测试。MockMvcRequestBuilders可以进行get或post请求，使用print()方法会将请求及相应的过程都打印出来（见以下第二段代码块内容），其中 Body = Hello World! XXX ，可见请求成功。

```java
@SpringBootTest
public class TestHello {
    private MockMvc mockMvc;
    @Before
    public void setUp() {
        mockMvc = MockMvcBuilders.standaloneSetup(new HelloWorldController()).build();
    }
    @Test
    public void testHello() throws Exception {
        MockHttpServletRequestBuilder accept = MockMvcRequestBuilders
        .post("/hello?name=XXX").accept(MediaType.APPLICATION_JSON_UTF8);
        mockMvc.perform(accept).andDo(print());
    }
}
```

```text
MockHttpServletRequest:
      HTTP Method = POST
      Request URI = /hello
       Parameters = {name=[XXX]}
          Headers = {Accept=[application/json;charset=UTF-8]}
Handler:
             Type = com.chung.springbootdemo.controller.HelloWorldController
           Method = public java.lang.String com.chung.springbootdemo.controller
           .HelloWorldController.helloWorld(java.lang.String)
Async:
    Async started = false
     Async result = null
Resolved Exception:
             Type = null
ModelAndView:
        View name = null
             View = null
            Model = null
FlashMap:
       Attributes = null
MockHttpServletResponse:
           Status = 200
    Error message = null
          Headers = {Content-Type=[application/json;charset=UTF-8], Content-Length=[16]}
     Content type = application/json;charset=UTF-8
             Body = Hello World! XXX
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
Process finished with exit code 0
```

----------
 3 热部署：可能有时候一丁点改动都需要重启项目，这个时候，我们可以使用SpringBoot的热部署特性（需要依赖主键Devtools）。主要有以下四个步骤，①在dependency中加入optional属性，并设为true。②在plugin中配置另外一个属性fork，并设为true。③在IDEA中勾选Build project automatically，如下第一张图。④按快捷键ctrl+shift+a输入Registry，勾选选项compile.automake.allow.when.app.running，如下第一张和第二章图。
至此，已经开启了SpringBoot热部署，可以尝试修改代码，发现刷新页面即可，再也不需要重新部署代码了。

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-devtools</artifactId>
	<optional>true</optional><!--加入这行-->
	<scope>runtime</scope>
</dependency>
```

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<!--加入以下三行-->
			<configuration>
                   <fork>true</fork>
			</configuration>
		</plugin>
	</plugins>
</build>
```

 ![9](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-9.png)

 ![10](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-10.png)

 ![11](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/5-11.png)



&emsp;&emsp;至此，就完成了Spring Boot项目的搭建和简单Web开发与测试、以及设置IDEA的热部署。可以看出，使用Spring Boot可以非常方便快速的搭建项目，而不用关心框架之间的兼容性、版本适用性等各种问题。想使用任何东西，仅仅添加一个配置就可以，所以Spring Boot非常适合构建微服务。

&emsp;&emsp;本系列的下一篇博客，小博将记录自己的Spring Boot Web开发速体验备忘录，学习永无止境！一起加油！

----------


