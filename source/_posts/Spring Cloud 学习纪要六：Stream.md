---
title: Spring Cloud Stream
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-24 22:11:29
---

> Spring Cloud Stream 是一个构建消息驱动微服务的框架，应用程序通过 input 或者 output 来与 Spring Cloud Stream 中 binder 交互，而 binder 负责与消息中间件交互。

<!-- more -->

开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.0.6
Spring Cloud| Finchley.SR2
Docker | 18.09.0
RabbitMQ| 3.7.8-management

> **特别注意**：本系列纪要环环相扣，建议从第一节开始阅读&emsp;[点击跳转](http://zhangchong.xin/2018/11/20/Spring%20Cloud%20%E5%AD%A6%E4%B9%A0%E7%BA%AA%E8%A6%81%E4%B8%80%EF%BC%9AEureka/)

# 增加provider和consumer项目
## 添加依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
</dependency>
```

## 添加RabbitMQ配置
```yml
spring:
  rabbitmq:
    username: guest
    password: guest
    host: localhost
    port: 5672
```

# 普通RabbitMQ使用方式
## provider生产消息
```java
@RestController
@RequestMapping("/hello")
public class HelloController {
    @Autowired
    private AmqpTemplate amqpTemplate;

    @GetMapping("/send")
    public void sendMsg() {
        amqpTemplate.convertAndSend("myExchange", "myRoutingKey", "Hello MSG from provider");
    }
}
```
## consumer消费消息
```java
@Component
public class MQReceiver {
    @RabbitListener(bindings = @QueueBinding(
            exchange = @Exchange("myExchange"),
            key = "myRoutingKey",
            value = @Queue("myQueue")
    ))
    public void consume(String msg) {
        System.out.println("rabbit-" + msg);
    }
}
```

# Stream生产和消费消息
## provider生产消息
&emsp;&emsp;provider项目增加配置：
```yml
spring:
  cloud:
    stream:
      bindings:
        output:
          group: provider
          destination: input
          content-type: application/json
```
&emsp;&emsp;provider项目生产消息：
```java
@RestController
@RequestMapping("/hello")
@EnableBinding(Source.class)
public class HelloController {
    @Autowired
    private Source source;

    @GetMapping("/stream")
    public void stream() {
        source.output().send(MessageBuilder.withPayload("Stream Hello MSG from provider").build());
    }
}
```
## consumer消费消息
&emsp;&emsp;consumer项目增加配置：
```yml
spring:
  cloud:
    stream:
      bindings:
        input:
          group: consumer
          destination: input
          content-type: application/json
```

&emsp;&emsp;consumer项目消费消息：
```java
@Component
@EnableBinding(Sink.class)
public class MQReceiver {
    @StreamListener(Sink.INPUT)
    public void input(Message<String> message) {
        System.out.println("stream：" + message.getPayload());
    }
}
```

# 写在后面
&emsp;&emsp;不难发现，通过Stream可以极大简化消息队列的使用，不管是RabbitMQ还是Kafka，我们只需要关注Stream的使用，如此解耦也可以极大方便我们项目的消息中间件迁移。当然，本系列《Spring Cloud 学习纪要》小博只记录最简洁的入门Demo，更高阶的使用方法和技巧心得相信聪明的你能够自己掌握。

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)
