---
title: Java面试之技术框架
categories: 
  - 笔经面经
tags:
  - Java
  - 校招
date: 2018-08-28 15:08:16
---

> 笔经面经归纳汇总：技术框架
<!-- more -->
# Java面试之技术框架
## Spring  
### 简介
&emsp;&emsp;Spring 是一种用来简化企业级应用开发的开源框架，包括Spring Framework, Spring Data, Spring Security，Spring Boot，SpringMVC等。Spring 家族最核心的概念当属 AOP 和 IoC，详解见下节。其中 Spring 优点如下：
1. 降低了组件之间的耦合性 ，实现了软件各层之间的解耦
2. 可以使用便捷的众多服务，如事务管理，消息服务等
3. 容器提供了AOP技术，利用它很容易实现如权限拦截，运行期监控等功能
4. Spring对于主流的应用框架提供了集成支持，如Hibernate、JPA等
5. Spring属于低侵入式设计，代码的污染极低
6. Spring的高度开放性，开发者可以自由选择Spring的部分或全部

### AOP和IOC  
#### AOP(Aspect Oriented Programming，面向切面编程)
&emsp;&emsp;AOP简单说就是在目标方法执行前后自定义一些操作，一般都是基于代理模式来实现的，Spring支持两种代理模式，JDK原生代理和CGLib代理。AOP给程序带来良好的扩展性和封装性，可以实现业务代码与非业务代码的隔离。比如可以在不改变目标代码的前提下实现目标方法的增强：埋点业务处理、方法执行时间监控，打印日志，权限控制等等。
1. JDK动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。只能对实现了接口的类生成代理。
2. CGLib动态代理是利用ASM开源包，对代理对象类的Class文件加载进来，通过修改其字节码生成子类来处理。

- 切面(Aspect)：类是对物体特征的抽象，切面就是对横切关注点的抽象。
- 切点(Pointcut)：对连接点进行拦截的定义。
- 连接点(Joinpoint)：被拦截到的点，比如方法(Spring中一般是方法)、字段、构造器。
- 通知(Advice)：指拦截到连接点后要执行的代码，通知分为前置、后置、异常、最终、环绕五类。

> AOP：在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想

#### IoC(Inversion of Control，控制反转)  
&emsp;&emsp;IOC (Inversion of Control，控制反转)：对象之间的依赖关系由容器来创建。本来对象之间的关系是由开发者自己创建和维护的，在使用Spring框架后，对象之间的关系由容器来创建和维护，将开发者做的事让容器做，这就是控制反转。**BeanFactory接口是Spring Ioc容器的核心接口**。
&emsp;&emsp;DI (Dependecy Injection，依赖注入)：我们在使用Spring容器的时候，容器通过调用set方法或者是构造器来建立对象之间的依赖关系。注入方式有设值注入、构造注入、注解注入、接口注入(`基本不用`)，设值注入直观，自然；构造注入可以在构造器中决定依赖关系的顺序。

> 控制反转是目标，依赖注入是我们实现控制反转的一种手段。

### SpringMVC和Struts  
&emsp;&emsp;SpringMVC执行流程如下：  
  1. 客户端向Spring容器发起一个HTTP请求。
  2. 发起的请求被`前端控制器`(DispatcherServlet)拦截。
  3. 查询`处理器映射`（HandlerMapping）得到执行链，并请求相应的`处理器适配器`(HandlerAdapter)。
  4. 执行`处理器`(Handler)并处理请求，以ModelAndView（属性值和返回页面）的形式返回。此处Handler即平时编写的Controller。
  5. `前端控制器`查询`视图解析器`(ViewResolver)，并返回View。
  6. 成功渲染视图则返回给客户端，否则抛异常。

|   比较点    |       SpringMVC        |         Struts         |
|:----------:|:----------------------:|:----------------------:|
| 核心控制器  |    DispatcherServlet   |     FilterDispatcher    |
|  配置文件   |         量少（AOP）     | 量大（Interceptor机制）   |
| RESTful API|  易实现（方法级别）       |    实现费劲（类级别）     |
|处理Ajax请求 |@ResponseBody返回响应文本 |       拦截器集成Ajax     |
|    性能    |           稍快          |          稍慢           |

&emsp;&emsp;DispatcherServlet初始化流程如下：
1. 加载配置文件
2. 扫描所有的相关类
3. 初始化所有相关类的Class实例，并将其保存到IoC容器
4. 自动化的依赖注入(Autowired)
5. 初始化HandlerMapping

### Spring事务
#### 事务
&emsp;&emsp;数据库事务是指作为单个逻辑工作单元执行的一系列操作，要么完全地执行，要么完全地不执行。事务满足**原子性(Atomicity)**、**一致性(Consistency)**、**隔离性(Isolation)**、**持久性(Durability)** 四大特性。
> 详见： [Java 面试之数据库](https://blog.csdn.net/u012102104/article/details/79773362)
#### 事务管理  
&emsp;&emsp;Spring通过事务管理器接口`PlatformTransactionManager`为各平台如JDBC(`DataSourceTransactionManager`)、Hibernate(`HibernateTransactionManager`、`JpaTransactionManager`)等都提供了对应的事务管理器。  

#### 事务的传播特性  
&emsp;&emsp;当事务方法被另一个事务方法调用时，必须指定事务应该如何传播，根据如下代码中方法 A 有无事务，Spring定义了7种传播行为（默认为REQUIRED）：  
```java
@Transactional  
void A(){  
}  
@Transactional  
void B(){  
   A();  
} 
```
| 传播行为(简写) |	含义  |
|:--------:|:---:|
|**REQUIRED**|如果没有，就开启一个事务；如果有，就加入当前事务（方法A加入到方法B）|
|REQUIRES_NEW|如果没有，就开启一个事务；如果有，就将当前事务挂起|
|NESTED|如果没有，就开启一个事务；如果有，就在当前事务中嵌套其他事务（主事务提交或回滚，子事务也会提交或回滚）|
|SUPPORTS|如果没有，就以非事务方式执行；如果有，就使用当前事务|
|NOT_SUPPORTED|如果没有，就以非事务方式执行；如果有，就将当前事务挂起|
|NEVER|如果没有，就以非事务方式执行；如果有，就抛出异常|
|MANDATORY|如果没有，就抛出异常；如果有，就使用当前事务|

### Spring Boot  
&emsp;&emsp;Spring Boot来自于 Spring 大家族，是一套全新的框架，它默认帮我们进行了很多配置，集成了大量常用的第三方库（例如 Jackson、JDBC、MongoDB、Redis、Mail 等），这些第三方库几乎都可以开箱即用。Spring Boot可以帮助我们快速搭建一个项目，从而让开发者能够更加专注于业务逻辑。  

### Spring扩展  
 - 实现BeanPostProcess接口在Bean生成前后进行操作。
 - 实现BeanFactoryPostProcessor接口配置Bean元属性。
 - 实现FactoryBean接口定制个性化的Bean。

## MyBatis  
&emsp;&emsp;MyBatis是一款优秀的持久层框架，它几乎避免了所有的JDBC代码和手动设置参数以及获取结果集，它可以使用XML或注解来将接口和POJO映射成数据库中的记录。  
&emsp;&emsp;MyBatis与Spring集成的时候Spring提供了全局唯一的SqlSessionTemplate，SqlSessionTemplate 实现了SqlSession接口，那么如何保证多个线程调用同一个dao时拿到的SqlSession不会错乱呢？这个时候就用到了ThreadLocal，SqlSessionUtils.getSqlSession()会首先查看当前线程资源map有无SqlSession，有则返回无则新建然后返回，这样就能保证一条业务始终用的是同一个数据库连接，也就能正确处理数据库事务。

### MyBatis和Hibernate  
|    比较点   |	MyBatis    |          Hibernate       |
|:-----------:|:-------------:|:------------------------:|
|     特点    |半自动(手写SQL)| 全自动(根据映射生成SQL)  |
| SQL直接优化 |	  方便     |            复杂          |
| 数据库移植性|       弱      |             强           |
|   缓存机制  |	  欠缺     |            更优          |
|   日志系统  |	  欠缺     |            完整          |

### Statement和PreparedStatement的区别  

```java
/***PreparedStatement extends Statement***/

//Statement用法
sql1 = "select * from tbl_user where username='" + u + "' and password='" + p + "'";
statement = conn.createStatement();  
result1 = statement.executeQuery(sql1);  

//PrepareStatement用法
sql2 = "select * from tbl_user where username=? and password=?";  
prepareStatement = conn.prepareStatement(sql2);  
pstmt.setString(1, u);  
pstmt.setString(2, p);  
result2 = prepareStatement.executeQuery();
```

|    比较点   |	Statement    |          PreparedStatement       |
|:-----------:|:-------------:|:------------------------:|
|     用途    |执行静态SQL语句并返回结果| 执行已预编译SQL语句并返回结果|
|可读性| 低(字符串拼接) | 高(Set方法设值) |
|效率|低(字符串拼接)|高(占位符)|
|安全性|低(SQL注入)|高|

## 消息队列

1. 消息队列中间件是分布式系统中重要的组件，主要主要解决应用耦合、异步消息、流量削锋等问题，具有异步性、可靠性（存储到本地硬盘）、松耦合、分布式的特性。  
	- 主要特点是异步处理
	- 主要目的是减少请求响应时间、解耦
	- 主要使用场景是将比较耗时且不需同步返回结果的操作当做消息存入队列
2. 流量削峰的一种解决方案  
	- MQ推送模式改为定时或者批量拉取模式
	- 消息接收方实现批量处理等方式
3. RabbitMQ
	- RabbitMQ 是一个由 ErLang 开发的AMQP的开源实现。
	- 交换机（Exchange）的功能主要是接收消息并且转发到绑定的队列，交换机不存储消息，**交换机本质是一张路由查询表**。
		- Direct：绑定时设定一个路由键，消息的路由键匹配才会被投送到队列中。
		- Topic：根据模糊匹配转发消息（最灵活）。
		- Headers：设置头部参数类型的交换机。
		- Fanout：转发消息到所有绑定队列，消息广播的模式。

> 路由键（routing_key）在消息中，而绑定键（binding_key）作用于交换机和队列之间。当消息中的路由键和绑定键对应上的时候，交换机就知道将该消息存入哪个队列。

## 分布式
- 分布式：一个业务分拆多个子业务，部署在不同的服务器上（厨师和配菜师的关系）
- 集群：同一个业务，部署在多个服务器上（两个厨师的关系）

## 微服务
&emsp;&emsp;微服务架构风格是一种**使用一套小服务来开发单个应用**的方式，每个服务运行在自己的进程中，并使用轻量级机制通信（通常是`HTTP API`），这些服务能够通过自动化部署机制来独立部署、可以使用不同的编程语言实现、可以使用不同的数据存储技术，并保持最低限度的集中式管理。  
&emsp;&emsp;时下热门的微服务开发框架有：[Spring Cloud](http://projects.spring.io/spring-cloud)、[Dubbo](http://dubbo.io)


## RESTful  
&emsp;&emsp;**URL定位资源，HTTP动词描述操作**    

- GET：读取资源
- POST：创建资源
- PUT：更新资源
- DELETE：删除资源

> 使用PUT方式更新时，必须发送资源所有的属性


## Nginx
### 反向代理
- 正向代理：隐藏真实的请求客户端，服务端不知道真实的客户端是谁，正向代理服务器会代替客户端向服务器发送请求。**正向代理代理的对象是客户端**。

- 反向代理：隐藏真实的响应服务端，客户端不知道真实的服务端是谁，反向代理服务器会把请求转发到真实的服务器。**反向代理代理的对象是服务端**。

> 10086总机就是一种反向代理，客户不知道真正提供服务人的是谁。

### 负载均衡  
 - **四层负载均衡**：工作在OSI模型的**传输层**，它在接收到客户端的流量以后通过修改数据包的地址信息将流量转发到应用服务器，因此四层负载均衡的主要工作就是**转发**。
 - **七层负载均衡**：工作在OSI模型的**应用层**，七层负载均衡在接到客户端的流量以后，还需要一个完整的TCP/IP协议栈与客户端建立一条完整的连接，并将应用层的请求流量解析出来，再按照调度算法选择一个应用服务器，并与应用服务器建立另外一条连接将请求发送过去，因此七层负载均衡的主要工作就是**代理**。


## 设计模式  
### 设计模式的六大原则  
1. 单一职责原则：一个类只负责一个功能领域中的相应职责。
2. 开闭原则：一个软件实体应当对扩展开放，对修改关闭。
3. 里氏替换原则：所有引用父类的地方必须能透明地使用其子类的对象。
4. 依赖倒置原则：抽象不应该依赖于细节，细节应当依赖于抽象。(要针对接口编程，而不是针对实现编程)。
5. 接口隔离原则：使用多个专门的接口，而不使用单一的总接口。
6. 迪米特法则：一个软件实体应当尽可能少地与其他实体发生相互作用。

### 单例、工厂、观察者、适配器、责任链  
- 单例模式：一个类负责创建自己的对象，同时确保只有单个对象被创建，并提供一种访问其唯一对象的方式。
- 工厂模式：在创建对象时不暴露创建逻辑，并通过使用一个共同的接口来指向新创建的对象。
- 代理模式：一个类代表另一个类的功能，为其他对象提供一种代理以控制对这个对象的访问。
- 模板模式：一个抽象类公开定义了方法，它的子类可以按需要重写方法，但以抽象类中定义的模板调用。
- 策略模式：定义一系列的算法，把它们一个个封装起来,，并且使它们可相互替换。
- 过滤器模式：允许开发人员使用不同的标准来过滤一组对象，通过逻辑运算以解耦的方式把它们连接起来。
- 适配器模式：将一个类的接口转换成另一个接口，使原本由于接口不兼容而不能一起工作的类可以一起工作。
- 观察者模式：定义对象间的依赖关系，当一个对象的状态改变时，所有依赖于它的对象都被通知并自动更新。
- 责任链模式：为请求创建了一个接收者对象的链，沿着这条链传递请求，直到有对象处理它为止。
> 设计模式详见小博系列博客：[代码详解设计模式汇总](https://blog.csdn.net/u012102104/article/details/85328253)

### 写出生产者消费者模式  
&emsp;&emsp;生产者生产数据到缓冲区中，消费者从缓冲区中取数据。如果缓冲区已经满了，则生产者线程阻塞；如果缓冲区为空，那么消费者线程阻塞。  
```java
public class ProducerAndConsumer {
    static BlockingQueue resourceQueue = new LinkedBlockingQueue<Resource>(10);

    public static void main(String[] args) {
        Producer p = new Producer(); //生产者
        Consumer c1 = new Consumer(); //消费者1
        Consumer c2 = new Consumer(); //消费者2
        Consumer c3 = new Consumer(); //消费者3
        p.start();
        c1.start();
        c2.start();
        c3.start();
    }
}

/**
 * 资源
 */
class Resource {
    int id;

    public Resource(int id) {
        this.id = id;
    }
}

/**
 * 生产者
 */
class Producer extends Thread {
    int p = 1;

    @Override
    public void run() {
        while (true) {
            try {
                Resource resource = new Resource(p++);
                System.out.println("生产资源" + resource.id);
                ProducerAndConsumer.resourceQueue.put(resource);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

/**
 * 消费者
 */
class Consumer extends Thread {
    @Override
    public void run() {
        while (true) {
            try {
                System.out.println("消费资源" + 
                ((Resource) ProducerAndConsumer.resourceQueue.take()).id);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 高内聚、低耦合  
- 耦合性：也称块间联系。指软件各模块之间相互联系紧密程度的一种度量。模块之间联系越紧密，则其耦合性就越强。模块间耦合高低取决于模块间接口的复杂性、调用的方式及传递的信息。
- 内聚性：也称块内联系。指软件模块内部各元素彼此结合紧密程度的一种度量。模块内各元素（语名之间、程序段之间）联系越紧密，则其内聚性就越高。

> 高内聚、低耦合的系统具有更好的重用性，维护性，扩展性，可以更高效的完成系统的维护开发，持续的支持业务的发展，而不会成为业务发展的障碍
