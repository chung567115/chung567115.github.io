---
title: synchronized、volatile、lock详解
categories: 
  - Java
tags:
  - Java
date: 2018-04-18 11:02:28
---

> 在Java并发编程过程中，我们难免会遇到`synchronized`、`volatile`和`lock`，其中`lock`是一个类，而其余两个则是Java关键字。以下记录了小博开发过程中对这三者的理解

<!-- more -->
# synchronized、volatile、lock详解
> 关于线程与进程请参考博文 [以操作系统的角度述说线程与进程](http://blog.csdn.net/luoweifu/article/details/46595285)

## synchronized
&emsp;&emsp;`synchronized`是Java中的关键字，是一种同步锁。有以下几种用法：

1、修饰方法：在范围操作符之后，返回类型声明之前使用。每次只能有一个线程进入该方法，此时线程获得的是成员锁。
```java
public synchronized void syncMethod() {
	//doSomething
}
```

2、修饰代码块：每次只能有一个线程进入该代码块，此时线程获得的是成员锁。
```java
public int synMethod(int arg){
	synchronized(arg) {
		//doSomething
	}
}
```
3、修饰对象：如果当前线程进入，那么其他线程在该类所有对象上的任何操作都不能进行，此时当前线程获得的是对象锁。
```java
public class SyncThread implements Runnable {
	public static void main(String args[]) {
		SyncThread syncThread = new SyncThread();
		Thread therad1 = new Thread(syncThread, "therad1");
		Thread therad2 = new Thread(syncThread, "therad2");
		Thread therad3 = new Thread(syncThread, "therad3");
		therad1.start();
		therad2.start();
		therad3.start();
	}
	public void run() {
		synchronized (this) {
			System.out.println(Thread.currentThread().getName());
		}
	}
}
```
4、修饰类：如果当前线程进入，那么其他线程在该类中所有操作不能进行，包括静态变量和静态方法，此时当前线程获得的是对象锁。
```java
public class syncClass {
	public void method() {
		synchronized(syncClass.class) {
			//doSomething
		}
	}
}
```

## volatile
&emsp;&emsp;`volatile` 关键字的作用是**禁止指令的重排序**，强制从公共堆栈中取得变量的值，而不是从线程私有的数据栈中取变量的值。
`volatile`与`synchronized`的区别如下：

- `volatile` 不会发生线程阻塞，而 `synchronized` 会发生线程阻塞。
- `volatile` 只能修饰变量，而 `synchronized` 可以修饰方法、代码块等。
- `volatile` 不能保证原子性（不能保证线程安全），而 `synchronized` 可以保证原子性。
- `volatile` 解决的是变量在多线程之间的可见性，而 `synchronized` 解决的是多线程之间访问资源的同步性。

## lock
&emsp;&emsp;`synchronized`是*隐式锁*，在需要同步的对象中加入此控制，而`lock`是*显示锁*，需要显示指定起始位置和终止位置。

- 使用`lock`时在`finally`中必须释放锁，不然容易造成线程死锁；而使用`synchronized`时，获取锁的线程会在执行完同步代码后释放锁（或者JVM会在线程执行发生异常时释放锁）。
- 使用`lock`时线程不会一直等待；而使用`synchronized`时，假设A线程获得锁后阻塞，其他线程会一直等待。
- `lock`可重入、可中断、可公平也可不公平；而`synchronized`可重入但不可中断、非公平。
> 锁的类型请参考博文 [Java并发编程：Lock](http://www.cnblogs.com/dolphin0520/p/3923167.html)
