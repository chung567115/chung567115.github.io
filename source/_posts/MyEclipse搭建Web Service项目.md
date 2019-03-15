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

> [点击下载完整代码](http://download.csdn.net/download/u012102104/10172395)

① 创建Web Service Project。
![1](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-1.png)

② 新建类Hello
![6](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-2.png)

目录结构如下图
![2](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-3.png)

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
![3](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-4.png)

④新建另一个Web Service Project，选中项目，点击工具栏按钮-->New Web Service Client
![7](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-5.png)

⑤选择JAX-WS，Next
![5](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-6.png)

⑥填写WSDL文件URL(即前面服务端测试的url)，Next
![4](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-7.png)

⑦进度条加载完后，点击Finish。
![8](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-8.png)

⑧这时候可以看到，目录下自动创建了如下一些文件
![9](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/6-9.png)

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
