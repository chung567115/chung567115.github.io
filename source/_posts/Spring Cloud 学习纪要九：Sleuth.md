---
title: Spring Cloud Sleuth
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-26 22:10:00
---

> Spring Cloud Sleuth链路跟踪组件是一种分布式追踪解决方案，它兼容Zipkin。微服务架构由于服务单元数量众多、调用关系杂乱、业务复杂，如果出现了错误和异常很难去定位，所以微服务架构中，必须实现分布式链路追踪，去跟进一个请求到底有哪些服务参与，参与的顺序又是怎样的，让每个请求步骤直观可见，而 Sleuth + Zipkin 便可以帮助我们做到这样的事情。

<!-- more -->

# Spring Cloud Sleuth
开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.0.6
Spring Cloud| Finchley.SR2
Docker	|18.09.0
Zipkin| --

> **特别注意**：本系列纪要环环相扣，建议从第一节开始阅读&emsp;[点击跳转](http://zhangchong.xin/2018/11/20/Spring%20Cloud%20%E5%AD%A6%E4%B9%A0%E7%BA%AA%E8%A6%81%E4%B8%80%EF%BC%9AEureka/)

## Zipkin 容器搭建
```txt
docker run -d -p 9411:9411 openzipkin/zipkin
```
&emsp;&emsp;Docker下执行一个命令就能运行Zipkin容器，具体视个人PC情况而定，开启服务后访问[http://localhost:9411/zipkin/](http://localhost:9411/zipkin/)可以看到如下界面。
![Zipkin](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-9-1.png)

## 依赖添加
&emsp;&emsp;provider项目和consumer项目添加如下Maven依赖，**特别注意：**，因为我这边一直下载不到2.0.2的包，不知道是不是我的IDE的问题，将版本改为<kbd>2.1.0.M2</kbd>。
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
    <version>2.1.0.M2</version>
</dependency>
```

## 配置文件修改
&emsp;&emsp;provider项目和consumer项目添加如下配置：
```yml
spring:
  zipkin:
    base-url: http://localhost:9411/
    sender:
      type: web
  sleuth:
    sampler:
      probability: 1
logging:
  level:
    org.springframework.cloud.openfeign: debug
```

## 链路跟踪测试
&emsp;&emsp;访问consumer的接口[http://localhost:8091/hello/consume?num=1](http://localhost:8091/hello/consume?num=1)，发现consumer项目控制台会打印如下日志，注意`cde8ad1de4f90441`。
```json
2018-11-26 21:26:23.946  INFO [consumer,cde8ad1de4f90441,e3c14617dff44d76,true] 12488 --- [trix-provider-1] s.c.a.AnnotationConfigApplicationContext : Refreshing SpringClientFactory-provider: startup date [Mon Nov 26 21:26:23 CST 2018]; parent: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@777d0bc3
2018-11-26 21:26:24.009  INFO [consumer,cde8ad1de4f90441,e3c14617dff44d76,true] 12488 --- [trix-provider-1] f.a.AutowiredAnnotationBeanPostProcessor : JSR-330 'javax.inject.Inject' annotation found and supported for autowiring
2018-11-26 21:26:24.236  INFO [consumer,cde8ad1de4f90441,e3c14617dff44d76,true] 12488 --- [trix-provider-1] c.netflix.config.ChainedDynamicProperty  : Flipping property: provider.ribbon.ActiveConnectionsLimit to use NEXT property: niws.loadbalancer.availabilityFilteringRule.activeConnectionsLimit = 2147483647
2018-11-26 21:26:24.254  INFO [consumer,cde8ad1de4f90441,e3c14617dff44d76,true] 12488 --- [trix-provider-1] c.n.u.concurrent.ShutdownEnabledTimer    : Shutdown hook installed for: NFLoadBalancer-PingTimer-provider
2018-11-26 21:26:24.279  INFO [consumer,cde8ad1de4f90441,e3c14617dff44d76,true] 12488 --- [trix-provider-1] c.netflix.loadbalancer.BaseLoadBalancer  : Client: provider instantiated a LoadBalancer: DynamicServerListLoadBalancer:{NFLoadBalancer:name=provider,current list of Servers=[],Load balancer stats=Zone stats: {},Server stats: []}ServerList:null
2018-11-26 21:26:24.284  INFO [consumer,cde8ad1de4f90441,e3c14617dff44d76,true] 12488 --- [trix-provider-1] c.n.l.DynamicServerListLoadBalancer      : Using serverListUpdater PollingServerListUpdater
2018-11-26 21:26:24.313  INFO [consumer,cde8ad1de4f90441,e3c14617dff44d76,true] 12488 --- [trix-provider-1] c.netflix.config.ChainedDynamicProperty  : Flipping property: provider.ribbon.ActiveConnectionsLimit to use NEXT property: niws.loadbalancer.availabilityFilteringRule.activeConnectionsLimit = 2147483647
2018-11-26 21:26:24.315  INFO [consumer,cde8ad1de4f90441,e3c14617dff44d76,true] 12488 --- [trix-provider-1] c.n.l.DynamicServerListLoadBalancer      : DynamicServerListLoadBalancer for client provider initialized: DynamicServerListLoadBalancer:{NFLoadBalancer:name=provider,current list of Servers=[192.168.99.1:8081],Load balancer stats=Zone stats: {defaultzone=[Zone:defaultzone;	Instance count:1;	Active connections count: 0;	Circuit breaker tripped count: 0;	Active connections per server: 0.0;]
},Server stats: [[Server:192.168.99.1:8081;	Zone:defaultZone;	Total Requests:0;	Successive connection failure:0;	Total blackout seconds:0;	Last connection made:Thu Jan 01 08:00:00 CST 1970;	First connection made: Thu Jan 01 08:00:00 CST 1970;	Active Connections:0;	total failure count in last (1000) msecs:0;	average resp time:0.0;	90 percentile resp time:0.0;	95 percentile resp time:0.0;	min resp time:0.0;	max resp time:0.0;	stddev resp time:0.0]
]}ServerList:org.springframework.cloud.netflix.ribbon.eureka.DomainExtractingServerList@7900120d
```
&emsp;&emsp;访问Zipkin页面，查询consumer项目的调用链：
![1](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-9-2.png)
&emsp;&emsp;点击某一行即可查看该调用的详细信息，包括其traceId`cde8ad1de4f90441`。
![2](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-9-3.png)

## 总结
&emsp;&emsp;至此《Spring Cloud 学习纪要》系列博客就结束了，本系列博客介绍了包含服务治理组件Eureka、服务通信组件Feign、负载均衡组件Ribbon、配置中心组件Config、消息总线组件Bus、消息驱动组件Stream、统一网关组件Zuul、熔断降级组件Hystrix、链路跟踪组件Sleuth，此系列博客只适合入门小白，是一套极简教程，更多知识待您亲自探索。

组件   |  功能 |  同类框架
-------- | -----| -----
Spring Cloud Eureka   |   服务治理   | ZooKeeper、Consul
Spring Cloud Feign|   服务通信   | Retrofit、OKHttp、HttpClient
Spring Cloud Ribbon|   负载均衡   |  Nginx
Spring Cloud Config|   配置中心   |  Consul
Spring Cloud Bus|   消息总线   |  
Spring Cloud Stream|   消息驱动  |  
Spring Cloud Zuul|   统一网关   |  Janus、Spring Cloud Gateway
Spring Cloud Hystrix|   熔断降级   |  
Spring Cloud Sleuth|   链路跟踪   |  Zipkin、Pinpoint

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)
