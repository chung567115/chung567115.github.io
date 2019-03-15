---
title: Spring Cloud Ribbon
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-20 12:03:44
---

> Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端负载均衡的工具。它是一个基于HTTP和TCP的客户端负载均衡器，它可以通过在客户端中配置ribbonServerList来设置服务端列表去轮询访问以达到均衡负载的作用。

<!-- more -->

开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.1.0
Spring Cloud| Greenwich.M1

> **特别注意**：本系列纪要环环相扣，建议从第一节开始阅读&emsp;[点击跳转](http://zhangchong.xin/2018/11/20/Spring%20Cloud%20%E5%AD%A6%E4%B9%A0%E7%BA%AA%E8%A6%81%E4%B8%80%EF%BC%9AEureka/)

# 修改代码和配置
&emsp;&emsp;在IDEA的Run/Debug Configurations中，拷贝一份当前的运行配置，并将副本为VM options设置为<kbd>-DServer.port=8082</kbd>，为了区别不同实例接口的返回结果，新增IPConfig类：
```java
@Component
public class IPConfig implements ApplicationListener<WebServerInitializedEvent> {
    private int serverPort;
    @Override
    public void onApplicationEvent(WebServerInitializedEvent event) {
        this.serverPort = event.getWebServer().getPort();
    }
    public int getPort() {
        return this.serverPort;
    }
}
```
&emsp;&emsp;修改provider的HelloController代码：
```java
@RestController
@RequestMapping("/hello")
public class HelloController {
    @Autowired
    private IPConfig ipConfig;

    @GetMapping("/chung")
    public String helloChung(){
        return "Hello Chung By Provider on :" + ipConfig.getPort();
    }
}
```

# 运行两个provider实例和consumer项目
&emsp;&emsp;运行两个provider实例，发现访问[http://localhost:8081/hello/chung](http://localhost:8081/hello/chung)和[http://localhost:8082/hello/chung](http://localhost:8082/hello/chung)分别能得到`Hello Chung By Provider on :8081`和`Hello Chung By Provider on :8082`。
&emsp;&emsp;运行consumer项目，访问[http://localhost:8091/hello/consumer](http://localhost:8091/hello/consumer)，可以发现，我们不断刷新页面，结果将一直轮流为`Hello Chung By Provider on :8081`和`Hello Chung By Provider on :8082`。这是由于默认Ribbon的负载均衡策略为<kbd>RoundRobinRule</kbd>[轮询]。

# 修改负载均衡策略
&emsp;&emsp;在consumer项目的配置文件添加以下代码，重启项目之后，负载均衡策略将变为随机规则，可以发现结果将不再是线性轮流变化。其他策略也可根据具体需求调整。
```yml
#负载均衡策略
provider:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
```

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)
