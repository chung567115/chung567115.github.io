---
title: Spring Cloud Feign
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-20 11:10:59
---

> Spring Cloud微服务解决方案之服务通信组件

<!-- more -->

# Spring Cloud Feign
&emsp;&emsp;Spring Cloud Feign是简化Java HTTP客户端开发的工具，它的灵感来自于Retrofit、JAXRS-2.0和WebSocket。接下来将展示一个入门Demo。

开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.1.0
Spring Cloud| Greenwich.M1

> **特别注意**：本系列纪要环环相扣，建议从第一节开始阅读&emsp;[点击跳转](http://zhangchong.xin/2018/11/20/Spring%20Cloud%20%E5%AD%A6%E4%B9%A0%E7%BA%AA%E8%A6%81%E4%B8%80%EF%BC%9AEureka/)

## 新建项目
&emsp;&emsp;在IDEA中通过<kbd>Spring Initializr</kbd>创建项目，选择Spring Boot 2.1.0版本、选择Maven依赖Web、Eureka Discovery(Cloud Discovery中)和Feign(Cloud Routing中)。
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

## 加入注解
&emsp;&emsp;在入口类添加<kbd>@EnableEurekaClient</kbd>和<kbd>@EnableFeignClients</kbd>注解。
```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
public class FeignConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(FeignConsumerApplication.class, args);
    }
}
```

## 添加配置
&emsp;&emsp;在文件<kbd>application.yml</kbd>添加配置。
```yml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true
    instance-id: ${spring.cloud.client.ip-address}:${server.port}
    #    hostname: ConsumerName

spring:
  application:
    name: consumer
```

## 编写接口
&emsp;&emsp;在上一篇的provider项目中，新建简单的Controller，代码如下，运行provider项目，访问[http://localhost:8081/hello/chung](http://localhost:8081/hello/chung)可得到`Hello Chung By Provider`。
```java
@RestController
@RequestMapping("/hello")
public class HelloController {
    @GetMapping("/chung")
    public String helloChung(){
        return "Hello Chung By Provider";
    }
}
```

&emsp;&emsp;在本篇的新项目consumer中新建接口和Controller，代码如下：
```java
@FeignClient(name = "provider")
public interface ProviderClient {
    @GetMapping(value = "/hello/chung")
    String hello();
}
```
```java
@RestController
@RequestMapping("hello")
public class HelloController {
    @Autowired
    private ProviderClient providerClient;

    @GetMapping("/consumer")
    public String hello(){
        return providerClient.hello();
    }
}
```

## 运行项目
&emsp;&emsp;在VM options设置<kbd>-DServer.port=8091</kbd>，运行consumer项目，可以在Eureka页面看到本项目注册成功，访问[http://localhost:8091/hello/consumer](http://localhost:8091/hello/consumer)同样可以得到`Hello Chung By Provider`，证明服务间通信正常。
![Eureka](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-2-1.png)

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)
