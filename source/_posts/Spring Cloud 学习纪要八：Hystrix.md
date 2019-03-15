---
title: Spring Cloud Hystrix
categories: 
  - Spring Cloud
tags:
  - Java
  - Spring Cloud
  - 微服务
date: 2018-11-26 16:23:04
---

> Spring Cloud Hystrix熔断降级组件是Netflix开源的一个延迟和容错库，用于隔离访问远程系统、服务或第三方库，防止级联失败，从而提高系统的可用性与容错性。

<!-- more -->

# Spring Cloud Hystrix
开发环境    |  版本
-------- | -----
IDEA | 2018.2.6
JDK  | 1.8
Spring Boot| 2.0.6
Spring Cloud| Finchley.SR2

> **特别注意**：本系列纪要环环相扣，建议从第一节开始阅读&emsp;[点击跳转](http://zhangchong.xin/2018/11/20/Spring%20Cloud%20%E5%AD%A6%E4%B9%A0%E7%BA%AA%E8%A6%81%E4%B8%80%EF%BC%9AEureka/)

## consumer项目添加依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
```

## consumer项目添加配置
```yml
feign:
  client:
    config:
      default: #服务名，default为所有服务
        connectTimeout: 3000
        readTimeout: 5000
  hystrix:
    enabled: true #开启Feign熔断器

hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 10000
    hello: #方法名，default为所有方法
      execution:
        timeout:
          enable: true
        isolation:
          thread:
            timeoutInMilliseconds: 10000
      circuitBreaker:
        enabled: true
        requestVolumeThreshold: 10
        sleepWindowInMilliseconds: 10000
        errorThresholdPercentage: 50
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

## consumer项目修改代码
&emsp;&emsp;通过<kbd>@EnableCircuitBreaker</kbd>开启服务容错功能，通过<kbd>@EnableHystrixDashboard</kbd>开启服务容错大盘，实际上，被注释的三行可以通过<kbd>@SpringCloudApplication</kbd>来代替。
```java
//@SpringBootApplication
//@EnableEurekaClient
//@EnableCircuitBreaker
@SpringCloudApplication
@EnableFeignClients
@EnableHystrixDashboard
public class FeignConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(FeignConsumerApplication.class, args);
    }
}
```
&emsp;&emsp;通过<kbd>@HystrixCommand</kbd>注解来使用服务降级或服务熔断。provider项目正常运行时，访问[http://localhost:8091/hello/consume?num=1](http://localhost:8091/hello/consume?num=1)会得到正常返回值`Hello Chung By Provider on env:devDynamic and port:8081`，此即服务降级的现象。
&emsp;&emsp;停止provider项目的运行，再次访问[http://localhost:8091/hello/consume?num=1](http://localhost:8091/hello/consume?num=1)我们会得到`网络拥堵@Hystrix defaultFallback!`，访问[http://localhost:8091/hello/consume?num=2](http://localhost:8091/hello/consume?num=2)会得到`双数直接返回success`，此时我们再num=1页面不停按<kbd>F5</kbd>键刷新页面，保证刷新十次以上，此时立即访问[http://localhost:8091/hello/consume?num=2](http://localhost:8091/hello/consume?num=2)会发现居然得到了`网络拥堵@Hystrix defaultFallback!`，而等待几秒之后再刷新num=2页面却又能得到`双数直接返回success`，此即服务熔断的现象。
```java
@RestController
@RequestMapping("hello")
@DefaultProperties(defaultFallback = "defaultFallback")
public class HelloController {
    @Autowired
    private ProviderClient providerClient;

    // 降级
//    @HystrixCommand(
//            fallbackMethod = "helloFallback",
//            commandProperties = {
//                    @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")  //超时时间
//            }
//    )
    // 熔断
//    @HystrixCommand(
//            fallbackMethod = "helloFallback",
//            commandProperties = {
//                    @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),                //开启熔断
//                    @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),    //请求数达到后才计算
//                    @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"), //休眠时间窗
//                    @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "50"),    //错误率
//            }
//    )

    // 搭配配置文件写法
    @HystrixCommand
    @GetMapping("/consume")
    public String hello(@RequestParam Integer num) {
        if (num % 2 == 0) {
            return "双数直接返回success";
        }
        return providerClient.hello();
    }

    private String helloFallback(Integer num) {
        return "网络拥堵@Hystrix helloFallback! " + num;
    }

    private String defaultFallback() {
        return "网络拥堵@Hystrix defaultFallback!";
    }
}
```
> 为了测试方便，我们需要将provider项目中的hello接口添加Thread.sleep(2000);

## 服务容错统计大盘
&emsp;&emsp;访问[http://localhost:8091/hystrix](http://localhost:8091/hystrix)，填写相关必要信息点击Monitor Stream按钮即可进入统计大盘，相关信息不再赘述。
![初始页面](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-8-1.png)

![统计页面](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/spring-cloud-8-2.png)

<kbd>**附件**</kbd>
> 本系列纪要博客源码：[跳转到github](https://github.com/chung567115/SpringCloud)
> 本系列纪要博客配置文件：[跳转到github](https://github.com/chung567115/SpringCloudConfig)
