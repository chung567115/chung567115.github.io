---
title: Spring Cloud Zuul
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-25 20:14:23
---

> Spring Cloud微服务解决方案之统一网关组件

<!-- more -->

# Spring Cloud Zuul
&emsp;&emsp;Spring Cloud Zuul 提供动态路由，监控，弹性，安全等边缘服务，将细粒度的服务组合起来提供一个粗粒度的服务，所有请求都导入一个统一的入口，那么整个系统只需要暴露一个服务，对外屏蔽服务端实现细节，这样也减少了客户端与服务器的网络调用次数，这就是api gateway，也是Zuul可以帮我们实现的功能。

开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.0.6
Spring Cloud| Finchley.SR2

> **特别注意**：本系列纪要环环相扣，建议从第一节开始阅读&emsp;[点击跳转](http://zhangchong.xin/2018/11/20/Spring%20Cloud%20%E5%AD%A6%E4%B9%A0%E7%BA%AA%E8%A6%81%E4%B8%80%EF%BC%9AEureka/)

## 路由
### 新建项目zuul
&emsp;&emsp;Maven依赖：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```
&emsp;&emsp;配置文件（可以参照前几章内容将配置放到git仓库）：
```yml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/,http://localhost:8762/eureka/
  instance:
    prefer-ip-address: true
    instance-id: ${spring.cloud.client.ip-address}:${server.port}
    #    hostname: ZuulName

spring:
  application:
    name: zuul
  cloud:
    config:
      discovery:
        enabled: true
        service-id: config
      profile: dev
```
&emsp;&emsp;启动类添加<kbd>@EnableZuulProxy</kbd>注解：
```java
@SpringBootApplication
@EnableZuulProxy
public class ZuulApplication {
    public static void main(String[] args) {
        SpringApplication.run(ZuulApplication.class, args);
    }
}
```
&emsp;&emsp;在前面几章的项目均已启动的前提下，将zuul项目VM options设置<kbd>-DServer.port=8061</kbd>，运行成功后访问[http://localhost:8061/provider/hello/chung](http://localhost:8061/provider/hello/chung)，发现zuul会自动将请求路由至provider服务。
> 此路径中得到provider为serviceId

### 自定义路由
&emsp;&emsp;进行如下配置，运行成功后访问[http://localhost:8061/myRoute/hello/chung](http://localhost:8061/myRoute/hello/chung)，zuul也会自动将请求路由至provider服务。
```yml
zuul:
  routes:
    provider: /myRoute/**
```

## PRE_TYPE Zuul Filter
&emsp;&emsp;前置过滤器可以实现限流、鉴权、参数校验等功能。
### 鉴权、参数校验
```java
@Component
public class PreFilter extends ZuulFilter {
    @Override
    public String filterType() {
        return PRE_TYPE;
    }

    @Override
    public int filterOrder() {
        return PRE_DECORATION_FILTER_ORDER - 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() {
        RequestContext currentContext = RequestContext.getCurrentContext();
        HttpServletRequest request = currentContext.getRequest();

        // 鉴权
        if ("/provider/hello/send".equals(request.getRequestURI())) {
            if (false) {
                currentContext.setSendZuulResponse(false);
                currentContext.setResponseStatusCode(HttpStatus.SC_UNAUTHORIZED);
            }
        }

        // 参数校验
        if (StringUtils.isEmpty(request.getParameter("token"))) {
            currentContext.setSendZuulResponse(false);
            currentContext.setResponseStatusCode(HttpStatus.SC_UNAUTHORIZED);
        }
        return null;
    }
}
```

### API限流
&emsp;&emsp;使用令牌桶算法，Google的RateLimiter工具。
```java
@Component
public class RateLimitFilter extends ZuulFilter {
    private static final RateLimiter RATE_LIMITER = RateLimiter.create(100);

    @Override
    public String filterType() {
        return PRE_TYPE;
    }

    @Override
    public int filterOrder() {
        return SERVLET_DETECTION_FILTER_ORDER - 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() {
        if (!RATE_LIMITER.tryAcquire()) {
            RequestContext currentContext = RequestContext.getCurrentContext();
            currentContext.setSendZuulResponse(false);
            currentContext.setResponseStatusCode(HttpStatus.SC_SERVICE_UNAVAILABLE);
        }
        return null;
    }
}
```

## POST_TYPE Zuul Filter
&emsp;&emsp;后置过滤器可以实现统计、日志记录等功能。
```java
@Component
public class PostFilter extends ZuulFilter {

    @Override
    public String filterType() {
        return POST_TYPE;
    }

    @Override
    public int filterOrder() {
        return SEND_RESPONSE_FILTER_ORDER - 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() {
        RequestContext currentContext = RequestContext.getCurrentContext();
        HttpServletResponse response = currentContext.getResponse();
        /**Some Code**/
        response.setHeader("POST_HEADER", UUID.randomUUID().toString());
        return null;
    }
}
```

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)
