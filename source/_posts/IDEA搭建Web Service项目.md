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
![1](http://img.blog.csdn.net/20171225164339192?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![2](http://img.blog.csdn.net/20171225164401972?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2、项目建成后，会有如下文件。
![3](http://img.blog.csdn.net/20171225164410962?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

3、在HelloWorld.java上右键-->WebService-->Generate WSDL From Java Code，生成对应的wsdl文件，见下三图。
![4](http://img.blog.csdn.net/20171225164419523?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![5](http://img.blog.csdn.net/20171225164428846?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![6](http://img.blog.csdn.net/20171225164441495?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

4、配置项目Tomcat，Run-->Edit Configurations-->添加-->Tomcat Server-->Local，如下图。
![7](http://img.blog.csdn.net/20171225164450204?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

5、命名tomcat，选择Deployment页，进行如下几图操作。
![8](http://img.blog.csdn.net/20171225164459748?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![9](http://img.blog.csdn.net/20171225164509947?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![10](http://img.blog.csdn.net/20171225164518882?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

6、File-->Project Structure，按序进行如下四图操作。
![11](http://img.blog.csdn.net/20171225164529095?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![12](http://img.blog.csdn.net/20171225164541509?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![13](http://img.blog.csdn.net/20171225164552304?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![14](http://img.blog.csdn.net/20171225164603757?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

7、上述步骤完成后，启动Tomcat，访问 http://localhost:8080/services 若出现如下界面，则部署成功。
![15](http://img.blog.csdn.net/20171225164614699?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

8、下面进行Web Service项目客户端的代码生成。新建WebService Client项目，如下两图。
![16](http://img.blog.csdn.net/20171225164624693?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![17](http://img.blog.csdn.net/20171225164637056?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

9、Web service wsdl url选项框中，选择前面搭好的Web Service服务端HelloWorld.wsdl文件。如下图。
![18](http://img.blog.csdn.net/20171225164648206?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

新建成功后，目录结构以及生成的文件如下图。
![19](http://img.blog.csdn.net/20171225164658295?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

10、File-->Project Structure，按序进行如下两图操作。
![20](http://img.blog.csdn.net/20171225164708936?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![21](http://img.blog.csdn.net/20171225171433143?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

11、编写测试文件TestHelloWorld.java，如下图所示。
![22](http://img.blog.csdn.net/20171225164721577?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

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
