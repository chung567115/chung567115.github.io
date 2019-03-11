---
title: Java中父类和子类的代码执行顺序
categories: 
  - Java
tags:
  - Java
date: 2018-05-02 14:12:13
---

> Java中，父类和子类的各种静态的、非静态的函数或变量，到底是个什么样的初始化顺序呢？

<!-- more -->
# Java中父类和子类的代码执行顺序
## 父类代码
```java
public class Parent {
    static {
        System.out.println("Parent static block");
    }

    {
        System.out.println("Parent non static block");
    }

    final static Value i = new Value("Parent static value");

    Value j = new Value("Parent non static value");

    Parent() {
        System.out.println("Parent Constructor");
    }
}

```

## 子类代码
```java
public class Child extends Parent {
    static {
        System.out.println("Child static block");
    }

    final static Value i = new Value("Child static value");

    {
        System.out.println("Child non static block");
    }

    Value j = new Value("Child non static value");

    Child() {
        System.out.println("Child Constructor");
    }
}

```

## 变量类代码
```java
public class Value {
    public Value(String value) {
        System.out.println(value);
    }
}

```

## 测试代码
```java
public class Main {
    public static void main(String[] args) {
        Child child = new Child();
    }
}

```

## 打印结果
```xml
Parent static block
Parent static value
Child static block
Child static value
Parent non static block
Parent non static value
Parent Constructor
Child non static block
Child non static value
Child Constructor
```

## 分析总结
&emsp;&emsp;单从结果可以看出，实例化一个子类对象时，程序步骤如下：
1. 执行父类静态代码块和静态变量
2. 执行子类静态代码块和静态变量
3. 执行父类非静态代码块和非静态变量
4. 执行父类构造函数
5. 执行子类非静态代码块和非静态变量
6. 执行子类构造函数

&emsp;&emsp;可以得出顺序：父类静态 $\rightarrow$ 子类静态 $\rightarrow$ 父类非静态 $\rightarrow$ 父类构造函数 $\rightarrow$ 子类非静态 $\rightarrow$ 子类构造函数

> 注意：以上流程3和5步骤中具体的执行顺序，是由代码书写顺序决定的
