---
title: IDEA搭建Web Service项目
categories: 
  - Java
tags:
  - Java
  - Web Service
date: 2017-12-25 16:49:54
---

> 本篇博客，小博将记录IDEA下，简单Web Service项目搭建的详细过程

<!-- more -->
# IDEA搭建Web Service项目

1、新建Web Service服务端项目，见下两图
![1](/7-1.png)

![2](/7-2.png)

2、项目建成后，会有如下文件。
![3](/7-3.png)

3、在HelloWorld.java上右键-->WebService-->Generate WSDL From Java Code，生成对应的wsdl文件，见下三图。
![4](/7-4.png)

![5](/7-5.png)

![6](/7-6.png)

4、配置项目Tomcat，Run-->Edit Configurations-->添加-->Tomcat Server-->Local，如下图。
![7](/7-7.png)

5、命名tomcat，选择Deployment页，进行如下几图操作。
![8](/7-8.png)

![9](/7-9.png)

![10](/7-10.png)

6、File-->Project Structure，按序进行如下四图操作。
![11](/7-11.png)

![12](/7-12.png)

![13](/7-13.png)

![14](/7-14.png)

7、上述步骤完成后，启动Tomcat，访问 http://localhost:8080/services 若出现如下界面，则部署成功。
![15](/7-15.png)

8、下面进行Web Service项目客户端的代码生成。新建WebService Client项目，如下两图。
![16](/7-16.png)

![17](/7-17.png)

9、Web service wsdl url选项框中，选择前面搭好的Web Service服务端HelloWorld.wsdl文件。如下图。
![18](/7-18.png)

新建成功后，目录结构以及生成的文件如下图。
![19](/7-19.png)

10、File-->Project Structure，按序进行如下两图操作。
![20](/7-20.png)

![21](7-21.png)

11、编写测试文件TestHelloWorld.java，如下图所示。
![22](/7-22.png)

其中代码为
```java
public class TestHelloWorld {
    public static void main(String[] args) throws Exception {
        HelloWorld_PortType helloWorld_portType = new HelloWorldServiceLocator().getHelloWorld();
        String result = helloWorld_portType.sayHelloWorldFrom("Charles");
        System.out.println(result);
    }
}
```

运行TestHelloWorld，可见控制台打印：Hello, world, from Charles。
至此，IDEA下简单Web Service项目的搭建就完成了。 

----------
