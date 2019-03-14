---
title: 【置顶】Spring Boot快速入门
categories: 
  - Spring Boot
tags:
  - Java
  - Spring Boot
  - 微服务
date: 2018-01-14 00:00:01
top: 4
---

> 适合小白的Spring Boot快速入门教程

<!-- more -->

# Spring Boot快速入门
&emsp;&emsp;相信小伙伴们在使用Spring或SpringMVC的过程中，或多或少会被各种配置问题所牵绊，这些配置不仅徒增工作量，而且在跨平台部署时容易出现问题。因此，Spring Boot应运而生，它集成了大量常用的第三方库配置（例如Jackson、JDBC、MongoDB、Redis、Mail等等），这些第三方库几乎都可以零配置的开箱即用，可以帮助我们快速搭建一个项目，从而让开发者能够更加专注于业务逻辑。
&emsp;&emsp;Spring Boot的优点及特性，就不再逐一罗列了，本场Chat针对Spring Boot的初学者，让我们先快速学会怎么用，至于更深入的探讨，留待后续Chat进行。

## 版本列表
名称            | 版本    | 	备注     	
:------------: | :---:  | :-------------: 
 Spring Boot    | 1.5.9  |	
 JDK            | 1.8.0  | 	  
 MySQL          | 5.7.15 |  	
 IDEA Ultimate  | 2017.2 | 不能用社区版	    
 Maven          | 3.5.2  |	可用阿里仓库提速   	 

## 快速构建项目
&emsp;&emsp;关于Spring Boot项目的构建，IDEA为我们提供了良好的支持，在IDEA中New-->Project-->Spring Initializr-->Next-->Next(可自己填写相关信息)-->选中Web栏目的Web依赖-->Next-->Finish(可自己填写相关信息)，一个简单Spring Boot项目搭建好了。没错不用惊讶，搭好了。接下来我们看看IDEA自动生成目录结构（以读者填写的具体信息为准）。

- main
  - java
    - xin.chung.springboot
      - SpringbootApplication.java(项目入口文件)
  - resources
    - static（静态资源包）
    - templates（页面模板包）
    - application.properties（项目属性配置文件）
- test
  - java
    - xin.chung.springboot
      - SpringbootApplicationTests.java（测试文件）

&emsp;&emsp;IDEA自动为我们生成了几个文件，其中application.properties文件为项目属性配置文件，一般包含服务器、数据库、日志等方面的配置，以及一些自定义的属性。SpringbootApplicationTests.java是JUnit测试类，用来进行开发过程中的单元测试。这两类文件，留待后面详细介绍。而至于这个SpringbootApplication.java文件，它其实是项目的入口文件，内容也非常简单，如下：

```java
@SpringBootApplication
public class SpringbootApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }
}
```

&emsp;&emsp;当我们运行这个类或主方法时，框架就会自动为我们开启服务。若见日志打印如下，说明启动成功，从日志可以看到，我们的Tomcat 8080端口已经准备就绪。

``` vim
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.9.RELEASE)

······

Tomcat started on port(s): 8080 (http)
Started SpringbootApplication in 4.694 seconds (JVM running for 7.789)
```

> 关于项目构建途径或项目启动的其他方式，大家可在读者圈一起交流。

## 项目属性配置
### 单元测试
&emsp;&emsp;在后续话题开始之前，我们有必要先探讨下单元测试。以往我们写一段测试代码，有一个前置步骤：用`ClassPathXmlApplicationContext`装载上下文，然后用`ApplicationContext`对象获取Bean实例。接下来，才能进行我们的测试代码编写。
&emsp;&emsp;而在Spring Boot中，极大简化了测试代码，它为我们提供了`@RunWith`注解和`@SpringBootTest`注解分别用来指定测试运行类和运行Spring Boot基础测试环境。此时，你可以直接使用`@Resource`或`@Autowired`注入所需Bean并使用，示例如下：

``` java
@RunWith(SpringRunner.class)
@SpringBootTest
public class PropertiesTests {
    @Resource
    private DemoService demoService;//仅作展示，还未编写

    @Test
    public void test() {
        System.out.println(demoService.getDemo().toString());
    }
}
```

> 由于单元测试贯穿本场Chat，所以每个章节都会在相应的地方讨论Spring Boot中JUnit的具体使用方法，此处只写出最简单的使用示例

### 属性配置
&emsp;&emsp;在前面提到过的application.properties文件中，我们可以进行一些项目的配置，也可以自定义一些项目所需要的属性参数然后用`@Value`注解在Java文件中引用，例如：

``` properties
# Server 服务相关
server.port=8080
server.context-path=/demo
# Log 日志相关
logging.path=/var/logs
logging.file=demo.log
# Other 自定义
author=chung
addr=http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1
```

&emsp;&emsp;我们可以编写如下测试代码，验证`.properties`文件中的值是否能被我们在Java文件中所使用，至于打印结果我就不贴出来了。

``` java
@RunWith(SpringRunner.class)
@SpringBootTest
public class PropertiesTests {
    @Value("${author}")
    private String author;
    @Value("${addr}")
    private String addr;

    @Test
    public void test() {
        System.out.println("author:" + author + "--addr:" + addr);
    }
}
```

&emsp;&emsp;实际上，我们也可以使用YAML作为项目的配置文件，这种语言更加简洁明了。以上`.properties`文件，可等价如下`.yml`文件（**切记`:`后面的空格不能少，同时注意每行的缩进**）：

``` yml
# Server 服务相关
server:
  port: 8080
  context-path: /demo
# Log 日志相关
logging:
  path: /var/logs
  file: demo.log
# Other 自定义
author: chung
addr: http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1
```

&emsp;&emsp;但是以上两种写法都有同一个弊端：项目里面自定义配置项往往会有很多，频繁使用`@Value`注入将给项目的后期维护带来极大不便。这个时候，我们就可以建立一个属性对象，利用`@ConfigurationProperties`注解来绑定属性，有效简化繁杂的注入操作。例如：

``` yml
# Other
gitchat:
  author: chung
  addr: http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1
```

``` java
@Component
// prefix对应配置文件中的前缀
@ConfigurationProperties(prefix = "gitchat")
public class GitChat {
    private String author;
    private String addr;
	
    /***省略Getter Setter***/
}
```
&emsp;&emsp;同理，我们可以编写如下测试代码，验证这种属性对象的方式能否正常使用。

``` java
@Resource
private GitChat gitChat;

@Test
public void test() {
    System.out.println("author:" + gitChat.getAuthor() + " addr:" + gitChat.getAddr());
}
```

&emsp;&emsp;可以看到，当属性越来越多，我们利用对象的方式优势也就越大，而至于`.properties`和`.yml`选哪种，就看个人喜好了。

## 控制器基本用法
### 关于Controller
&emsp;&emsp;讨论完了项目的属性配置，让我们探讨一下在Spring Boot中如何使用控制器。在Spring Boot Controller的编码中，我们常用到的注解如下：
- 标注控制层组件的注解`@Controller`、`@RestController`
- 配置URL映射的注解`@RequestMapping`
- 获取参数的注解`@PathVariable`(URL中)、`@RequestParam`(HTTP请求中)
- 校验参数的注解`@Valid`

&emsp;&emsp;相信以上那些，大家或多或少有点熟悉，其实还有一些简洁的组合注解，这些注解在配合使用`RESTful API`时，会非常优雅非常有逼格：
- `@GetMapping("")`
- `@PostMapping("")`
- `@PutMapping("")`
- `@DeleteMapping("")`

&emsp;&emsp;而实际上，`XXXMapping("YYY")` 和 `@RequestMapping(value = "YYY",method = RequestMethod.XXX)`没有区别，点进源码一切你就会恍然大悟。
&emsp;&emsp;看完这些注解，接下来我们瞧瞧怎么把它们用进控制器。先拿`@Controller`和`@RestController`来说，如果使用`@Controller`注解，表示控制器返回的是模板，而使用`@RestController`注解，则表示返回的可能是JSON或文本等等。
&emsp;&emsp;见如下代码，编写完成后启动项目，浏览器访问[http://localhost:8080/demo/say](http://localhost:8080/demo/say)，你会看到Hello World！，就是这么简单粗暴，什么Jackson配置之类的，让框架替你解决就好了。

``` java
@RestController
public class DemoController {
    @RequestMapping("/say")
    public String sayHello() {
        return "Hello World!";
    }
}
```

> **注意**：
> 1. 项目配置文件选择application.properties与application.yml之一即可(删掉其中一个)
> 2. 若上一章中没有配置server.context-path=/demo或者context-path: /demo，则应访问[http://localhost:8080/say](http://localhost:8080/say)
> 3. 添加完依赖后，右键pom.xml-->Maven-->Reimport，重新导入

&emsp;&emsp;至于`@Controller`注解，我们常用来和模板引擎`Thymeleaf`一起使用，这里我们不讨论模板引擎，简单看看`@Controller`的使用：
1. 在pom文件中导入`Thymeleaf`依赖(IDEA的提示功能很强大，好好利用)
2. 编写Controller代码，返回hello页面模板
3. 编写页面模板hello.html

``` xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

```java
@Controller
public class DemoController {
    @RequestMapping("/say")
    public String sayHello() {
        return "hello";
    }
}
```

``` html
<html>
    <head>
        <title>Hello</title>
    </head>
    <body>
        <p>Hello World!</p>
    </body>
</html>
```

&emsp;&emsp;同样，启动项目，访问[http://localhost:8080/demo/say](http://localhost:8080/demo/say)，我们发现，Hello World!还是那个Hello World!，但是它们背后的故事可就大不相同了。

> 实际上，我们点进源码就会发现，`@RestController`其实就是`@ResponseBody`＋`@Controller`，玩到后来还是被套路了...

&emsp;&emsp;再来看看控制器中其它注解的使用，就拿那几个代表性的传说中和`RESTful`完美搭配的那几个家伙为例，我们在`@RestController`注解标识的控制器中，编写如下方法：

```java
@GetMapping("/getGitChat/{id}")
public GitChat getGitChat(@PathVariable String id) {
    GitChat gitChat = new GitChat(null, "chung", new Date(), "http://gitbook.cn/gitchat/activity/" + id);
    return gitChat;
}
```

&emsp;&emsp;其中`@GetMapping`表示此方法仅响应GET请求，其请求路径也附在括号内。而`@PathVariable`则获取了路径中的一个参数，这个参数在路径中用大括号标注。
&emsp;&emsp;还要再编写实体类GitChat如下：

```java
public class GitChat {
    private Integer id;
    private String author;
    private Date date;
    private String url;

    /***省略NoArgsConstructor AllArgsConstructor***/
    /***省略Getter Setter***/
}
```

&emsp;&emsp;此时重启项目，再访问[http://localhost:8080/demo/getGitChat/5a444c451f6ee91a25846ac1](http://localhost:8080/demo/getGitChat/5a444c451f6ee91a25846ac1)，就可以看到框架已经自动帮我们把对象转换为JSON显示在了浏览器，如下：

```json
{
    "id": null,
    "author": "chung",
    "date": 1514896266452,
    "url": "http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1"
}
```

&emsp;&emsp;在上面的例子中，我们用到了`@PathVariable`注解，它可以帮助我们从路径中获取参数。而与其不同的`@RequestParam`注解，它可以帮助我们从请求中获取参数，例如：

```java
@GetMapping("/getGitChat")
public GitChat getGitChatByRequestParam(@RequestParam(defaultValue = "5a444c451f6ee91a25846ac1") String id) {
    GitChat gitChat = new GitChat(null, "chung", new Date(), "http://gitbook.cn/gitchat/activity/" + id);
    return gitChat;
}
```

&emsp;&emsp;此时重启项目，访问[http://localhost:8080/demo/getGitChat?id=5a444c451f6ee91a25846ac1](http://localhost:8080/demo/getGitChat?id=5a444c451f6ee91a25846ac1)也将得到同样的结果，

> **注意**：如果缺少`?id=*`这段，访问时将会报错，为避免这样的问题，我们可以设置默认值`@RequestParam(defaultValue = "5a444c451f6ee91a25846ac1")`。

### 单元测试
&emsp;&emsp;在上一章我们讨论了Spring Boot中JUnit的简单写法，其实，我们可以使用IDEA插入单元测试，这样更加便捷，省去了我们新建包、新建文件、新建类的时间，步骤如下：
1. 在代码中选中`DemoController`右键-->Go To-->Test-->Create New Test-->勾选需要测试的方法-->OK
2. 这时IDEA已经在test目录下相应的包内为我们创建好了`DemoControllerTest`文件
3. 我们使用Spring提供的`MockMvc`类来完成测试，用`@AutoConfigureMockMvc`注解让框架自动配置，并使用`@Resource`注入MockMvc
4. 编写接口测试代码并打印，测试代码及结果如下两段

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class DemoControllerTest {
    @Resource
    private MockMvc mockMvc;

    @Test
    public void getGitChatByRequestParam() throws Exception {
        mockMvc.perform(
            MockMvcRequestBuilders
                .get("/getGitChat")
                .param("id", "5a444c451f6ee91a25846ac1")
        )
            .andDo(print())
            .andReturn();
    }
}
```

```yml
MockHttpServletResponse:
       Status = 200
Error message = null
      Headers = {Content-Type=[application/json;charset=UTF-8]}
 Content type = application/json;charset=UTF-8
         Body = {"author":"chung","date":1514893447007,"url":"http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1"}
Forwarded URL = null
Redirected URL = null
      Cookies = []
```

&emsp;&emsp;测试结果限于篇幅，我只贴出了一小部分，在真是测试日志中我们可以看到各种详细的数据，包括返回页面Body体的具体内容也打印了出来。这样的测试代码简单优雅而又霸气十足有木有。

## 数据库基本操作
### 关于Repository
&emsp;&emsp;看完了与前端交互的部分，我们来讨论下Spring Boot在数据库操作方面，提供了哪些逆天的功能。
&emsp;&emsp;提到持久化，不得不提`JPA(Java Persistence API)`，jpa是一种规范或者说标准，`Hibernate`很好的实现了它，而我们使用到的`Spring-Data-JPA`则是`Spring`对`Hibernate`的整合。探讨实例之前，我们先做好如下准备：

&emsp;&emsp;添加`Spring-Data-JPA`的依赖，如下，**注意：spring-boot-starter-data-jpa千万不要写错**。

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

&emsp;&emsp;做好数据库相关配置，url和用户名密码等。其中`ddl-auto: update`表示对于表结构，有则更新，无则创建，`show-sql: true`则表示在控制台打印执行的SQL语句。

```yml
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/dbgirl
    username: root
    password: root
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
```

&emsp;&emsp;我们最好还是自己提前建好相关数据库和表，SQL语句如下：

```sql
CREATE TABLE `tbl_gitchat` (
  `id` int(8) unsigned NOT NULL AUTO_INCREMENT,
  `author` varchar(6) DEFAULT '',
  `date` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
  `url` varchar(255) DEFAULT '',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

&emsp;&emsp;此时，对与前面提到的GitChat实体类，我们又有必要进行一定的改造，看下面这段，其中`@Entity`注解表示实体类与数据库表的映射关系，`@Id`标识主键，`@GeneratedValue`标识生成规则，默认为AUTO，这些都是常见的了。

```java
@Entity(name = "tbl_gitchat")
public class GitChat {
    @Id
    @GeneratedValue
    private Integer id;
    private String author;
    private Date date;
    private String url;

    /***省略NoArgsConstructor AllArgsConstructor***/
    /***省略Getter Setter***/
    /***省略Override toString***/
}
```

&emsp;&emsp;完成了以上步骤，接下来的重头戏，当然是我们的DAO层代码啦。你没看错，仅仅只是将接口继承自`JpaRepository`就好了，<1,2>中，1为实体类，2为主键类型。至此，我们便可以进行简单的数据库CURD操作了，简单不简单？粗暴不粗暴？心动不心动？

```java
public interface GitChatRepository extends JpaRepository<GitChat, Integer> {

}
```

### 单元测试
&emsp;&emsp;话说回来，还是来测试测试DAO层代码吧。首先我们提前插入几条测试数据到到数据库中备用：

```sql
INSERT INTO `tbl_gitchat` VALUES ('1', 'chung', '2018-01-02 21:07:10', 'http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1');
INSERT INTO `tbl_gitchat` VALUES ('2', 'chung', '2018-01-02 21:07:33', 'http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1');
INSERT INTO `tbl_gitchat` VALUES ('3', 'charle', '2018-01-02 21:07:47', 'http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1');
```

&emsp;&emsp;接着，根据提到过的方法，自动生成测试文件，并编写代码如下，其中`findAll()`是框架为我们提供的查询所有数据的方法，它会返回一个实体对象的List。

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class GitChatRepositoryTest {
    @Resource
    private GitChatRepository gitChatRepository;

    @Test
    public void testFindAll(){
        List<GitChat> all = gitChatRepository.findAll();
        System.out.println(all.toString());
    }
}
```

&emsp;&emsp;我们可以看看测试结果，打印出的内容正是我们所插入的3条记录。

```vim
[GitChat{id=1, author='chung', date=2018-01-02 21:07:10.0, url='http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1'}, 
GitChat{id=2, author='chung', date=2018-01-02 21:07:33.0, url='http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1'}, 
GitChat{id=3, author='charle', date=2018-01-02 21:07:47.0, url='http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1'}]
```

&emsp;&emsp;你也许会问，大工程里面，这么复杂的逻辑，仅仅使用框架提供的能够满足特定需求吗。不要怕，忘了告诉你，更逆天的是，JPA也支持自定义的数据库操作甚至自定义SQL语句。举个例子如下，简简单单定义好方法之后，你就可以使用它来根据`author`查找所有满足条件的`GitChat`了。**特别要注意的是**，这个类和方法的名字可不是随便起的，它们的名称必须遵循`Spring Data JPA`的命名规范，而关于这个命名规范，不在本场Chat内容中，大家可以自行查阅资料，就不在此赘述了。

```java
public interface GitChatRepository extends JpaRepository<GitChat, Integer> {
    List<GitChat> findGitChatsByAuthor(String author);
}
```

&emsp;&emsp;同样，我们来测试下，这么简单的定义了一个方法之后，是否真的能够如愿满足我们的需求：

```java
@Test
public void findGitChatsByAuthor() throws Exception {
    List<GitChat> byAuthor = gitChatRepository.findGitChatsByAuthor("chung");
    System.out.println(byAuthor.toString());
}
```

&emsp;&emsp;可以从打印出的内容中很明显的看到，这一次只有两个`author`值为`chung`的记录，说明我们的写法是可以完成查询特定条件的内容的。

```vim
[GitChat{id=1, author='chung', date=2018-01-02 21:07:10.0, url='http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1'}, 
GitChat{id=2, author='chung', date=2018-01-02 21:07:33.0, url='http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1'}]
```

&emsp;&emsp;以上我们只是简单地讨论了JPA的使用，其他的CURD操作，大家可以举一反三。实际上JPA的功能远不止如此，我们可以自己写实现类来自定义操作，也可以自己写`SQL`语句配合`@Query`来查询，基本上来说，只有你想不到，没有它办不到。是不是霸气侧漏？

## 事务管理简介
### 关于Service
&emsp;&emsp;前面我们讨论了和前端打交道的控制器，也讨论了和数据库打交道的持久层，是时候讨论业务层了。与Controller类似，Service类，也有一个自己的注解，叫做`@Service`，配合我们曾编写过的`GitChatRepository`，我们可以编写一个简单的Service Demo，其中使用到了GitChatRepository的`save()`，这个方法也是框架为我们提供的，代码如下：

```java
@Service
public class GitChatService {
    @Resource
    private GitChatRepository gitChatRepository;

    public void saveGitChat() {
        GitChat gitChat1 = new GitChat(null, "Zhang", new Date(), "http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1");
        gitChatRepository.save(gitChat1);
        GitChat gitChat2 = new GitChat(1, "ZhangChong", new Date(), "http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1");
        gitChatRepository.save(gitChat2);
    }
}
```

&emsp;&emsp;这个时候我们测试`GitChatService`的`saveGitChat()`，代码如下：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class GitChatServiceTest {
    @Resource
    private GitChatService gitChatService;

    @Test
    public void saveGitChat() throws Exception {
        gitChatService.saveGitChat();
    }
}
```

&emsp;&emsp;测试完成后可以发现，数据库内只增加了一条记录。没关系，还记得我们前面设计数据库的时候，特意将`author`字段长度设置为6，这里由于我们插入的第二个实体对象`author`字段超长不符要求，所以导致第二条数据插入失败了，但是这并不影响我们判断：框架为我们提供的`save()`确实可用，而且一般情况下，添加异常处理之后，简单的Service层代码这么写，是没有问题的。

### 关于事务管理
&emsp;&emsp;很多时候，我们的业务层逻辑复杂，很多操作都需要在同一事务中进行，这个时候，`@Transactional`注解就派上了用场。
它可以配置事务传播特性，默认是`REQUIRED`，`@Transactional`的使用也非常简单，你只需要在你的方法上加上这个注解便可：

```java
@Service
public class GitChatService {
    @Resource
    private GitChatRepository gitChatRepository;

    @Transactional
    public void saveGitChat() {
        GitChat gitChat1 = new GitChat(null, "Zhang", new Date(), "http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1");
        gitChatRepository.save(gitChat1);
        GitChat gitChat2 = new GitChat(1, "ZhangChong", new Date(), "http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1");
        gitChatRepository.save(gitChat2);
    }
}
```

&emsp;&emsp;依旧使用`saveGitChat()`测试方法，只不过这一次我们测试的方法添加了`@Transactional`注解。

```vim
Caused by: com.mysql.jdbc.MysqlDataTruncation: Data truncation: Data too long for column 'author' at row 1
    at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3971)
    at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3909)
    at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:2527)
    at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:2680)
    at com.mysql.jdbc.ConnectionImpl.execSQL
    ......
```

&emsp;&emsp;测试完我们发现，这一次日志中报错了，错误信息为`Data too long for column 'author' at row 1`。我们再去数据库查看，发现并没有任何新增的记录。对比上一次的测试结果可以知道，`@Transactional`将我们的两次插入操作置于同一事务中，当第二条数据插入失败时，进行了回滚操作，所以数据库不会有新增的记录。

## 拓展进阶
### 表单验证
&emsp;&emsp;在接口开发的过程中，我们经常需要对提交的各种表单数据进行验证，这个时候，我们可以借助`@Valid`注解来轻松达到目的。话不多说，我们直接来看。再次改造GitChat实体类，在`author`字段上添加`@Size(min = 1, max = 6)`，他为我们限定了`author`字段的长度为1-6，在`url`字段上添加`@NotNull(message = "url不能为空")`，它为我们限制了url不能为null，并定义了错误信息。

```java
@Entity(name = "tbl_gitchat")
public class GitChat {
    @Id
    @GeneratedValue
    private Integer id;

    @Size(min = 1, max = 6)
    private String author;

    private Date date;

    @NotNull(message = "url不能为空")
    private String url;

    /***省略NoArgsConstructor AllArgsConstructor***/
    /***省略Getter Setter***/
    /***省略Override toString***/
}
```

&emsp;&emsp;接下来在控制器中，我们这么使用`@Valid`注解：

```java
@RestController
public class DemoController {
    @Resource
    private GitChatService gitChatService;

    @PostMapping("/gitChat")
    public Object addGitChat(
        @Valid GitChat gitChat,
        BindingResult bindingResult
    ) {
        if (bindingResult.hasErrors()) {
            return bindingResult.getFieldError().getDefaultMessage();
        } else {
            return gitChatService.saveGitChat(gitChat);
        }
    }
}
```

&emsp;&emsp;以上`@Valid`伴随的`BindingResult`对象中，携带校验的各种信息。我们可以利用这个对象很方便的判定表单是否验证通过。让我们测试测试：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class DemoControllerTest {
    @Resource
    private MockMvc mockMvc;

    @Test
    public void testAddGitChat() throws Exception {
        mockMvc.perform(
            MockMvcRequestBuilders
                    .post("/gitChat")
                    .param("author", "ZhangChong")
                    .param("date", String.valueOf(new Date()))
                    .param("url", "http://gitbook.cn/gitchat/activity/5a444c451f6ee91a25846ac1")
        )
            .andDo(print())
            .andReturn();
    }
}
```

&emsp;&emsp;从上面的测试代码中看到，我们故意调用接口插入一条不符规则的记录。然后从下面测试日志的`Body = size must be between 1 and 6`可以看出，提交的参数校验失败了，最后再去看看数据库，发现并没有成功添加记录。这一系列的现象或结果说明一个很简单的道理：`@Valid`为我们立下了汗马功劳，简化了我们各种判断与校验的工作。

```vim
MockHttpServletResponse:
       Status = 200
Error message = null
      Headers = {Content-Type=[text/plain;charset=UTF-8], Content-Length=[28]}
 Content type = text/plain;charset=UTF-8
         Body = size must be between 1 and 6
Forwarded URL = null
Redirected URL = null
      Cookies = []
```

## 写在后面
&emsp;&emsp;本场Chat旨在快速入门Spring Boot，了解相关的基础操作，因此未进行深入的探讨，如果发布之后效果还行的话，以后会考虑推出进阶的Chat或课程，有兴趣的小伙伴可以关注下。另外，本场Chat完整的代码已分享至GitHub[点击访问](https://github.com/chung567115/springboot)，可自行下载。
&emsp;&emsp;码字不易，期待你的好评~

> 欢迎打赏，啊哈哈~
