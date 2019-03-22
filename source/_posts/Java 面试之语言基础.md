---
title: Java面试之语言基础
categories: 
  - 笔经面经
tags:
  - Java
  - 校招
date: 2018-08-28 15:07:49
---

> 笔经面经归纳汇总：Java基础

<!-- more -->

# Java八种基本数据类型
|  类 型  |  大小 | 封 装 类 |
|:-------:|:-----:|:--------:|
|   byte  | 1字节 |   Byte   |
|  short  | 2字节 |   Short  |
|   int   | 4字节 |  Integer |
|   long  | 8字节 |   Long   |
|  float  | 4字节 |   Float  |
|  double | 8字节 |  Double  |
|   char  | 2字节 | Character|
| boolean |   --  |  Boolean |

# Java面向对象（四个基本特性）  
1. **封装**：将客观事物包装成类，隐藏具体实现，提供操作接口。
2. **继承**：子类获得父类的属性和行为(`extends`)。
3. **多态**：同一行为多种表现形式。
	- 重写`Override`
	- 接口的不同实现`implements`
	- 抽象类和抽象方法`abstract`
4. **抽象**：用代码描述客观事物。包括数据抽象(属性)和过程抽象(行为)。

# Java与C++对比  
|   比较点   |        Java         |           C++         |
|:----------:|:-------------------:|:---------------------:|
|面向对象思想|      完全对象化     |可采用非面向对象(兼容C)|
|内存管理机制|     Java自身管理    |        程序员管理     |
|  异常机制  |         完善        |           欠缺        |
|  第三方库  |丰富(Log、JUnit等等) |        较少(STL)      |
|  执行效率  |          慢         |            快         |
|  操控底层  |         麻烦        |           方便        |

# Java 8新特性  
- **默认方法**：在接口里面有了一个实现的方法
- Lambda 表达式：Lambda允许把函数作为一个方法的参数
- Stream API：把真正的函数式编程风格引入到Java中
- Date Time API：加强对日期与时间的处理
- Optional 类：Optional 类已成为 Java 8类库的一部分，用来解决空指针异常
- 方法引用：可以直接引用已有Java类或对象的方法或构造器
- 新编译工具：Nashorn引擎（一个新的JavaScript引擎）、 类依赖分析器

# Java反射机制  
&emsp;&emsp;在运行状态中，对于任意一个类，都能够知道其所有属性和方法；对于任意一个对象，都能够调用其任意方法和属性。这种动态获取信息及动态调用对象方法的功能称为Java语言的反射机制。Java反射API如下：  

- Class：反射的核心类。
- Field：类的成员变量。 
- Method：类的成员方法。 
- Constructor：类的构造方法。

```java
/**获取Class对象的三种方式**/

// 1. 调用某个对象的getClass()方法
Class clazz = person.getClass();

// 2. 调用某个类的class属性
Class clazz = Person.class;

// 3. 使用Class类的forName()静态方法
Class clazz = Class.forName("Person");
```
&emsp;&emsp;反射机制主要提供了以下功能：
1. 在运行时判断任意一个对象所属的类。
2. 在运行时构造任意一个类的对象。
3. 在运行时判断任意一个类所具有的成员变量和方法。
4. 在运行时调用任意一个对象的方法。
5. 生成动态代理。

# Java异常机制  
## Error与Excption包结构  
1. Error：程序无法处理的错误，继承自Throwable。
2. Exception：程序可以处理的异常，继承自Throwable。
    1. Checked Exception：**受检异常，需要强制catch**，是Exception的子类。
    2. Unchecked Exception：**不受检异常，无需强制catch**，是RuntimeException的子类。

	> 例如`list.size()`语句，若list为null，则会出现`NullPointerException`(Unchecked Exception)

```java
if (ex instanceof RuntimeException){
    // ex is unchecked exception
} else if (ex instanceof Exception){
    // ex is checked exception
} else {
    // ex is a error
}
```

![Throwable体系](https://raw.githubusercontent.com/chung567115/chung567115.github.io/hexo-blog/blog-img/34-1.png)

# 各种比较
## Override和Overload的区别  
|  比较点  |   Override   |   Overload    |
|:-------:|:------------:|:-------------:|
|   中文   |     重写     |     重载       |
|  方法名  |     相同     |     相同       |
|   形参   |     相同     |     不同       |
| 返回类型  |     相同     |   可同可不同    |

> Override 典型例子：接口方法的重写
> Overload 典型例子：构造方法的重载

## Interface与abstract类的区别  
|   比较点   |    Interface   |   abstract类  |
|:---------:|:--------------:|:-------------:|
|    中文    |      接口      |     抽象类     |
| 能否实例化  |      不能      |     不能       |
|方法能否实现 |   Java 8 可以  |     可以       |
| 一个类可以  |`implements`多个|  `extends`一个 |

## equals()与==的区别  
&emsp;&emsp;`equals()`用于比较对象(内容)，`==`常用于比较原生类型(地址)。简单的说，`==`比较两个人的DNA，`equals()`一般用来比较两个人身高体重等等，可自定义。
```java
int x = 10;
int y = 10;
String str1 = "abc";
String str2 = "abc";
String str3 = new String("abc");
String str4 = new String("abc");

// == 比较两个(基本数据类型)地址值
System.out.println(x == y); // true
System.out.println(str1 == str2); // true
System.out.println(str3 == str4); // false

// String重写的equals()比较两个对象内容
System.out.println(str1.equals(str2)); // true
System.out.println(str3.equals(str4)); // true
```
> 要注意Object类中`equals()`即为`==`，`equals()`是可以重写(`Override`)的。  

## String、StringBuffer、StringBuilder  
|    比较点      |       String     | StringBuffer |StringBuilder |
|:--------------:|:----------------:|:------------:|:------------:|
|   底层实现     |final char value[]| char[] value | char[] value |
|可变性(参考底层)|      不可变      |     可变     |     可变     |
|   修改时       | **不会**改变自身 |**会**改变自身|**会**改变自身|
|   安全性       |     线程安全     |   线程安全   |  非线程安全  |
|   使用场景     |     少量数据     |多线程大量数据|单线程大量数据|

```java
String str = "abc"; // str只是一个String对象的引用
str = "xyz"; // 创建了新对象"xyz"，而引用str重新指向新对象
```

## 强弱软虚四种引用  
|  比较点  |   强引用  |   软引用 |   弱引用      |   虚引用   |
|:--------:|:----------:|:----------:|:----------:|:----------:|
|  特性  | 不会被回收 | 内存足则不回收，内存不足则回收| 一旦发现便回收  | 必须和引用队列联合使用  |
|  使用场景  |平时工作接触的最多(`new`) |   可用来实现内存敏感的高速缓存    |  常用于Map数据结构中，引用占用内存空间较大的对象   |   跟踪对象被回收的活动   |

## comparable接口和comparator接口  
- Comparable：在集合内部实现排序的接口，位于java.lang包。
- Comparator：在集合外部实现排序的比较器接口，位于java.util包。

```java
public class Person implements Comparable {
    String name;
    int age;
    
    @Override
    public int compareTo(Object o) {
        // 使用字符串的比较
        int i = name.compareTo(((Person) o).name); 
        
        if (i == 0) { // 如果名字一样,比较年龄, 返回比较年龄结果
            return age - ((Person) o).age;
        } else {
            return i; // 名字不一样, 返回比较名字的结果.
        }
    }
}

//使用Collections.sort(personList)即可排序
```

```java
public class Person {
    String name;
    int age;

    static class PersonComparator implements Comparator {
        @Override
        public int compare(Object o1, Object o2) {
            // 使用字符串的比较
            int i = ((Person) o1).name.compareTo(((Person) o2).name); 
            
            if (i == 0) { // 如果名字一样,比较年龄,返回比较年龄结果
                return ((Person) o1).age - ((Person) o2).age;
            } else {
                return i; // 名字不一样, 返回比较名字的结果.
            }
        }
    }
}

//使用Collections.sort(list, new Person.PersonComparator())即可排序
```
> Arrays内使用双轴快排(DualPivotQuicksort)，两个中心点三段数据。

## IO，BIO，NIO，AIO
&emsp;&emsp;Java中IO是以流为基础进行输入输出的，在网络编程中，接触到最多的就是利用Socket进行网络通信开发，主要有BIO、NIO、AIO三种实现方式。  

|   比较点   |    BIO   |     NIO    |     AIO     |
|:----------:|:--------:|:----------:|:-----------:|
|    中文    |  阻塞IO  |  非阻塞IO  |   异步IO    |
|     版本   |   --     | JDK 1.4提出|JDK 1.7 提出 |
|    描述   |服务端每次都需要创建一个线程来建立连接并处理消息。若建立连接、读写数据时发生阻碍，线程会阻塞。并发情况下，N个连接需要N个线程来处理。|使用一个线程来管理所有的Socket 通道，也就是基于Selector机制，查询到事件时(连接、读写)，转发给不同的处理线程(Handler)。  |在进行读写操作时，只需要调用相应的read/write方法，并传入CompletionHandler(动作完成处理器)，在动作完成后会调用CompletionHandler。 |

# 序列化与反序列化  
&emsp;&emsp;序列化即把对象转换为字节序列；反序列化即把字节序列恢复为对象。其使用场景大致有如下几种：  

- 把内存中的对象状态保存到文件或者数据库中。
- 用套接字在网络上传送对象。
- 通过RMI传输对象。

> JVM会把字节流中的serialVersionUID与本地相应实体类的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化。


# 哪些情况会导致OOM或SOF，怎么解决  
## OutOfMemory(OOM)  
- Java堆溢出：一般由于内存泄露或者堆的大小设置不当引起。可以通过虚拟机参数`-Xms`、`-Xmx`等设置堆大小。
- 方法区溢出：包括运行时常量池溢出，一般由于大量动态生成的Class导致。可以通过`-XX:PermSize`和`-XX:MaxPermSize`限制方法区的大小。
> 使用JConsole生成Heap Dump文件，然后使用MAT分析排查

## StackOverflow(SOF)  
- Java虚拟机栈和本地方法栈内存溢出：一般是由于程序中存在死循环或者深度递归调用造成的，栈设置太小也会出现此种溢出。可以通过虚拟机参数`-Xss`来设置栈大小。


# JNI的使用  
&emsp;&emsp;JNI (Java Native Interface)允许Java代码和其他语言代码(主要是C和C++)进行交互。  
1. 定义本地native方法
2. 用javah命令生成.h头文件，拷贝至jni目录
3. 在jni目录中编写.c文件
4. 在jni目录中创建Android.mk文件，声明所引用的.c文件和生成的函数库名
5. 创建Application.mk文件，声明支持的平台(armeabi、armeabi-v7a、x86)
6. 使用ndk工具编译生成.so成动态链接库文件

