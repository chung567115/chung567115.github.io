---
title: Java面试之结束问答
categories: 
  - 笔经面经
tags:
  - Java
  - 校招
date: 2018-08-28 15:12:53
---

> 笔经面经归纳汇总：结束问答
<!-- more -->
# Java面试之结束问答
## 技术优化
### 线程池优化  
- 设置最大线程数
- 设置最小核心线程数
- 设置额外线程存活时间
- 选择线程池队列
- 选择合适的线程池
- 选择合适的饱和策略

### 锁优化  
- 尽量不要锁住方法
- 缩小同步代码块，只锁数据
- 锁中尽量不要再包含锁
- 将锁私有化，在内部管理锁
- 进行适当的锁分解

### HTTP 1.1 $\rightarrow$ HTTP 2.0  
- Tomcat 在 server.xml 里配置HTTP 协议版本
```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11AprProtocol"
            maxThreads="150" SSLEnabled="true">
    <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol"/>
    <SSLHostConfig>
        <Certificate certificateKeyFile="conf/localhost-rsa-key.pem"
           certificateFile="conf/localhost-rsa-cert.pem"
           certificateChainFile="conf/localhost-rsa-chain.pem" type="RSA"/>
    </SSLHostConfig>
</Connector>
```
- Spring Boot 在 application.properties 里配置HTTP 协议版本
```java
server.ssl.enabled=true
server.ssl.***
server.http2.enabled=true
server.tomcat.protocol-header-https-value=https
```

## 通过什么形式学习新技术  

## 还有什么要问的  


