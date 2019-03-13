---
title: synchronized与static synchronized的区别
categories: 
  - Java
tags:
  - Java
  - 并发编程
date: 2018-10-31 10:32:08
---

> 详解synchronized与static synchronized的区别

<!-- more -->

# synchronized与static synchronized的区别
- <kbd>synchronized</kbd>是实例锁，对类的**当前实例**进行加锁，防止其他线程同时访问该实例的**所有synchronized块**。

  > 注意这里是**类的当前实例**， 类的两个不同实例就没有这种约束了

- <kbd>static synchronized</kbd>是类锁，控制类的**所有实例**的并发访问，限制多线程中该类的**所有实例**同时访问JVM中该类所对应的代码块。

## 例题讲解
&emsp;&emsp;假如有Something类的两个实例x与y，那么下列各组方法被多线程同时访问的情况是怎样的？
```java
pulbic class Something{
    public synchronized void isSyncA(){}
    public synchronized void isSyncB(){}
    public static synchronized void cSyncA(){}
    public static synchronized void cSyncB(){}
}

A.  x.isSyncA() 与 x.isSyncB()   
B.  x.isSyncA() 与 y.isSyncA()  
C.  x.cSyncA() 与 y.cSyncB()  
D.  x.isSyncA() 与 Something.cSyncA()
```

**A：** 不能被同时访问，原因：是对相同实例<kbd>x</kbd>的不同<kbd>synchronized</kbd>域访问。

**B：** 可以被同时访问，原因：是对不同实例<kbd>x</kbd>和<kbd>y</kbd>的相同<kbd>synchronized</kbd>域访问。

**C：** 不能被同时访问，原因：是对不同实例<kbd>x</kbd>和<kbd>y</kbd>的不同<kbd>static synchronized</kbd>域访问。

**D：** 可以被同时访问，原因：是对实例<kbd>x</kbd>的<kbd>synchronized</kbd>域和类<kbd>Something</kbd>的<kbd>static synchronized</kbd>域同时访问。

## 总结
- <kbd>synchronized</kbd>锁的是实例对象，<kbd>static synchronized</kbd>锁的是类对象
- 若实例被锁，则**该实例**的**所有同步方法**全部被锁
- 若类被锁，则**该类**的**所有同步方法**全部被锁

> **实例锁与类锁之间互不干扰**
