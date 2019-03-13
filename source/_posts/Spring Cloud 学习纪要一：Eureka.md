---
title: Spring Cloud Eureka
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-20 10:27:52
---

> Spring Cloud微服务解决方案之服务治理组件

<!-- more -->

# Spring Cloud Eureka
&emsp;&emsp;Spring Cloud Eureka是Spring Cloud Netflix服务套件中的一部分，它基于Netflix Eureka做了二次封装，主要负责完成微服务架构中的服务治理，接下来将展示入门Demo。

开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.1.0
Spring Cloud| Greenwich.M1

## Eureka服务端
### 新建项目
&emsp;&emsp;在IDEA中通过<kbd>Spring Initializr</kbd>创建项目，选择Spring Boot 2.1.0版本、选择Maven依赖Eureka Server(Cloud Discovery中)。
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 加入注解
&emsp;&emsp;在入口类添加<kbd>@EnableEurekaServer</kbd>注解。
```java
@SpringBootApplication
@EnableEurekaServer // 开启Eureka注册服务
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```

### 添加配置
&emsp;&emsp;在文件<kbd>application.yml</kbd>添加配置。
```yml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
    register-with-eureka: false #禁止自身注册
#  server:
#    enable-self-preservation: false
  instance:
    prefer-ip-address: true  #默认显示IP
    instance-id: ${spring.cloud.client.ip-address}:${server.port}
    #    hostname: ServerName

spring:
  application:
    name: eureka
```

### 运行项目
&emsp;&emsp;在VM options设置<kbd>-DServer.port=8761</kbd>，运行项目，浏览器打开[http://localhost:8761](http://localhost:8761)即可看到Eureka页面。
![Eureka Server](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-1-1.png)

## Eureka客户端
### 新建项目
&emsp;&emsp;在IDEA中通过<kbd>Spring Initializr</kbd>创建项目，选择Spring Boot 2.1.0版本、选择Maven依赖Web和Eureka Discovery(Cloud Discovery中)。
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
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 加入注解
&emsp;&emsp;在入口类添加<kbd>@EnableEurekaClient</kbd>注解。
```java
@SpringBootApplication
@EnableEurekaClient //开启Eureka发现
public class FeignProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(FeignProviderApplication.class, args);
    }
}
```

### 添加配置
&emsp;&emsp;在文件<kbd>application.yml</kbd>添加配置。
```yml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true
    #    hostname: ProviderName
    instance-id: ${spring.cloud.client.ip-address}:${server.port}
spring:
  application:
    name: provider
```

### 运行项目
&emsp;&emsp;在VM options设置<kbd>-DServer.port=8081</kbd>，运行项目，在Eureka页面可以看到如下图线框处，证明本项目启动时已自动注册成功。
![EurekaClient](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-1-2.png)

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)
