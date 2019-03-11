---
title: Myeclipse搭建Web Service项目
categories: 
  - Java
tags:
  - Java
  - Web Service
date: 2017-12-25 14:48:16
---

> 本篇博客，小博将记录MyEclipse下，简单Web Service项目搭建的详细过程

<!-- more -->
# Myeclipse搭建Web Service项目

> [点击下载完整代码](http://download.csdn.net/download/u012102104/10172395)

① 创建Web Service Project。
![1](http://img.blog.csdn.net/20171225141857950?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

② 新建类Hello
![6](http://img.blog.csdn.net/20171225142007115?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

目录结构如下图
![2](http://img.blog.csdn.net/20171225142409137?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

代码如下

```java 
@WebService
public class Hello {
	public String sayHello(String name) {
		System.out.println("Method sayHello execute");
		return "Hello " + name;
	}
	public static void main(String[] args) {
		String address = "http://localhost:8080/Demo/Hello";
		Hello hello = new Hello();
		Endpoint.publish(address, hello);
		System.out.println("Web Service publish success");
	}
} 
```

③运行Hello.java(Run As Java Application)，可以看到控制台输出：Web Service publish success，可见启动成功。浏览器访问[http://localhost:8080/Demo/Hello?wsdl](http://localhost:8080/Demo/Hello?wsdl)，可以看到如下界面。
![3](http://img.blog.csdn.net/20171225141934617?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

④新建另一个Web Service Project，选中项目，点击工具栏按钮-->New Web Service Client
![7](http://img.blog.csdn.net/20171225142020098?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

⑤选择JAX-WS，Next
![5](http://img.blog.csdn.net/20171225141955102?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

⑥填写WSDL文件URL(即前面服务端测试的url)，Next
![4](http://img.blog.csdn.net/20171225141922081?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

⑦进度条加载完后，点击Finish。
![8](http://img.blog.csdn.net/20171225142031298?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

⑧这时候可以看到，目录下自动创建了如下一些文件
![9](http://img.blog.csdn.net/20171225142047394?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjEwMjEwNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

⑨新建TestHello.java文件调用服务端内容，代码如下

```java
public class TestHello {
	public static void main(String[] args) {
		Hello hello = new HelloService().getHelloPort();
		String result = hello.sayHello("Charles");
		System.out.println(result);
	}
}
```

⑩运行TestHello.java，可以看到控制台输出：Hello Charles。可见调用成功。

至此，简单的Web Service工程的服务端和客户端就搭建完成了。

----------
