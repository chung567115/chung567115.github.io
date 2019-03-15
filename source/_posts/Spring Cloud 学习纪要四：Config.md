---
title: Spring Cloud Config
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-22 15:36:19
---

> 普通应用中，当一个系统的配置文件发生改变时，需要重启该服务才能使新配置生效。而Spring Cloud Config可以实现微服务中所有系统配置文件的统一管理，当配置文件发生变化的时候，系统会自动更新获取新的配置。

<!-- more -->

# Spring Cloud Config
开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.1.0
Spring Cloud| Greenwich.M1

> **特别注意**：本系列纪要环环相扣，建议从第一节开始阅读&emsp;[点击跳转](http://zhangchong.xin/2018/11/20/Spring%20Cloud%20%E5%AD%A6%E4%B9%A0%E7%BA%AA%E8%A6%81%E4%B8%80%EF%BC%9AEureka/)

## 新建配置中心服务
&emsp;&emsp;在IDEA中通过<kbd>Spring Initializr</kbd>创建项目，选择Spring Boot 2.1.0版本、选择Maven依赖Eureka Discovery(Cloud Discovery中)和Config Server(Cloud Config中)。
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-config-server</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

## 加入注解
&emsp;&emsp;在入口类添加<kbd>@EnableEurekaClient</kbd>和<kbd>@EnableConfigServer</kbd>注解。
```java
@SpringBootApplication
@EnableEurekaClient
@EnableConfigServer
public class ConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigApplication.class, args);
    }
}
```

## 添加配置
&emsp;&emsp;在文件<kbd>application.yml</kbd>添加配置。
```yml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/ #,http://localhost:8762/eureka/
  instance:
    prefer-ip-address: true
    #    hostname: ProviderName
    instance-id: ${spring.cloud.client.ip-address}:${server.port}

#server:
#  port: 8071

spring:
  application:
    name: config
  cloud:
    config:
      server:
        git:
          uri: ${your_config_repository} #https://github.com/chung567115/SpringCloudConfig
          username: ${your_username}
          password: ${your_password}
          basedir: ${your_dir} # D:\IDEACode\Spring Cloud\config\basedir
```

## 上传配置文件
&emsp;&emsp;在自己的git仓库中添加配置文件项目，本例中将前几章中的provider和consumer项目的yml配置文件上传到[https://github.com/chung567115/SpringCloudConfig](https://github.com/chung567115/SpringCloudConfig)。

## 运行config项目
&emsp;&emsp;在VM options设置<kbd>-DServer.port=8071</kbd>，运行config项目，可以在Eureka页面看到本项目注册成功，访问[http://localhost:8071/provider-dev.yml](http://localhost:8071/provider-dev.yml)可以看到配置详情。实际上，我们访问xxxx/provider-dev.properties或xxxx/provider-dev.json，配置中心都将转换成对应格式的配置文件返回。其命名格式为<kbd>{name}-{profiles}.{type}</kbd>，其中name为文件名，一般定义为项目名；profiles为环境，例如开发环境、测试环境、生产环境等；type为文件格式，例如yml、properties、json等。
![yml](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-4-1.png)
![properties](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-4-2.png)
![json](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-4-3.png)

## 修改客户端代码
&emsp;&emsp;导入Spring Cloud Config客户端Maven依赖，在provider和consumer项目的pom.xml文件中加入以下代码并reimport：
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

&emsp;&emsp;为了能在项目启动的第一时间就应用远程git的配置，重命名provider和consumer项目的配置文件为bootstrap.yml，删除旧内容，保留必要信息。
```yml
spring:
  application:
    name: ${project_name} # provider/consumer
  # SpringCloudConfig
  cloud:
    config:
      discovery:
        enabled: true
        service-id: config
      profile: dev
```

## 运行provider或consumer项目
&emsp;&emsp;运行provider或consumer项目，发现项目依然能够正常注册和被发现，服务间也能正常通信，证明远程配置中心的内容生效。

> 以上只是达到了统一管理配置，关于修改配置之后如何免重启自动生效，小博将在《Spring Cloud 学习纪要五：Bus》中讲解。

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)
