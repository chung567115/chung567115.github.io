---
title: Spring Cloud Bus
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-24 16:58:46
---

> 上一篇我们讲到统一配置中心Spring Cloud Config，实现了分布式项目配置文件的统一管理，但是留下了一个问题：修改配置之后如何免重启自动生效。Spring Cloud Bus消息总线组件就可以用来做这件事，值得注意的是，前面我们所使用的版本<kbd>Greenwich.M1</kbd>的bus组件存在bug，我们将config和provider项目改为如下版本(其他项目建议一并修改)：

<!-- more -->

# Spring Cloud Bus
开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.0.6
Spring Cloud| Finchley.SR2
Docker | 18.09.0
RabbitMQ| 3.7.8-management

> **特别注意**：本系列纪要环环相扣，建议从第一节开始阅读&emsp;[点击跳转](http://zhangchong.xin/2018/11/20/Spring%20Cloud%20%E5%AD%A6%E4%B9%A0%E7%BA%AA%E8%A6%81%E4%B8%80%EF%BC%9AEureka/)

> bug官方github issue链接为[https://github.com/spring-cloud/spring-cloud-bus/issues/137](https://github.com/spring-cloud/spring-cloud-bus/issues/137)，可自行查阅。

## 准备工作
&emsp;&emsp;进行本章内容的学习前期需要准备好环境：Docker的安装、RabbitMQ容器的安装和运行、虚拟机IP到宿主机IP的映射等。
&emsp;&emsp;简而言之，访问[http://localhost:15672](http://localhost:15672)，应可以到达RabbitMQ管理界面，当然，5672端口的服务也需要能正常运行。
![MQ管理页面](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-5-1.png)

## 修改Config项目配置
### Maven依赖
&emsp;&emsp;在配置中心项目中添加bus组件的依赖，以及后续会用到的actuator和monitor依赖。
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-monitor</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
### 配置
&emsp;&emsp;暴露配置中心的各项接口，在配置文件添加如下内容：
```yml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

## 修改provider项目
&emsp;&emsp;我们以provider项目为例，添加bus组件的Maven依赖：
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```
&emsp;&emsp;修改接口为动态读取配置项：
```java
@RestController
@RequestMapping("/hello")
@RefreshScope // 使用该注解可在SpringCloudConfig配置刷新时自动更新本类配置
public class HelloController {
    @Value("${env}")
    private String env;

    @GetMapping("/chung")
    public String helloChung() {
        return "Hello Chung By Provider on env:" + env;
    }
}
```

## 验证配置项动态更新
&emsp;&emsp;**先后运行**config和provider项目（保证注册中心服务可用），发现RabbitMQ自动为我们创建了两个消息队列，如图所示。
![Queue](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-5-2.png)
&emsp;&emsp;此时我们访问provider的接口[http://localhost:8081/hello/chung](http://localhost:8081/hello/chung)可以得到`Hello Chung By Provider on env:dev`，接着我们修改git上的配置文件内容env的值`env : devDynamic`，然后用postman或curl命令以**post方式**访问config暴露的接口[http://127.0.0.1:8071/actuator/bus-refresh](http://127.0.0.1:8071/actuator/bus-refresh)，RabbitMQ的管理界面可以看到消息的消费情况，如图所示。
![MQ](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-5-3.png)
&emsp;&emsp;最后刷新[http://localhost:8081/hello/chung](http://localhost:8081/hello/chung)页面，发现内容变成了`Hello Chung By Provider on env:devDynamic`，证明我们实现了免重启修改配置。

## 配置GitHub的Webhooks
&emsp;&emsp;真实场景下，我们不可能每次修改配置后手动做一下post请求，而Webhook为我们提供了这样的功能。
&emsp;&emsp;如果本机IP为内网地址，可以提前准备一下内网穿透工具，视个人情况而定。
![NATAPP](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-5-4.png)
&emsp;&emsp;在我们的配置文件git仓库中，找到设置页面的Webhooks选项，将Payload URL设置为`${your_url}/monitor`，Content Type选择application/json，点击添加Webhook按钮。
![Webhook](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-5-5.png)
&emsp;&emsp;最后我们再修改git上的配置文件内容，稍候几秒刷新页面，发现能够真正实现免重启自动更新配置，详细内容和截图不再赘述。

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)


