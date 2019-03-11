---
title: Java中 List、Set、Map 之间的区别
categories: 
  - Java
tags:
  - Java
  - 数据结构
date: 2018-04-18 11:05:04
---

> 小博此篇记录了开发过程中常用的几种集合详解，三者的区别对比均从`IDEA`相关层次图里面所得知，基于`JDK8`。

<!-- more -->

# Java中 List、Set、Map 之间的区别
## List(列表)
&emsp;&emsp;`List`的元素以线性方式存储，可以存放重复对象，`List`主要有以下两个实现类：

- `ArrayList` : 长度可变的数组，可以对元素进行随机的访问，向`ArrayList`中插入与删除元素的速度慢。 `JDK8` 中`ArrayList`扩容的实现是通过`grow()`方法里使用语句`newCapacity = oldCapacity + (oldCapacity >> 1)`（即1.5倍扩容）计算容量，然后调用`Arrays.copyof()`方法进行对原数组进行复制。
- `LinkedList`: 采用链表数据结构，插入和删除速度快，但访问速度慢。 

## Set(集合) 
&emsp;&emsp;`Set`中的对象不按特定(HashCode)的方式排序，并且没有重复对象，`Set`主要有以下两个实现类：

- `HashSet`： `HashSet`按照哈希算法来存取集合中的对象，存取速度比较快。当`HashSet`中的元素个数超过数组大小*`loadFactor`（默认值为0.75）时，就会进行近似两倍扩容（`newCapacity = (oldCapacity << 1) + 1`）。
- `TreeSet` ：`TreeSet`实现了`SortedSet`接口，能够对集合中的对象进行排序。

## Map(映射)
&emsp;&emsp;`Map`是一种把键对象和值对象映射的集合，它的每一个元素都包含一个键对象和值对象。 `Map`主要有以下两个实现类：

- `HashMap`：`HashMap`基于散列表实现，其插入和查询`<K,V>`的开销是固定的，可以通过构造器设置容量和负载因子来调整容器的性能。 
- `LinkedHashMap`：类似于`HashMap`，但是迭代遍历它时，取得`<K,V>`的顺序是其插入次序，或者是最近最少使用(`LRU`)的次序。
- `TreeMap`：`TreeMap`基于红黑树实现。查看`<K,V>`时，它们会被排序。`TreeMap`是唯一的带有`subMap()`方法的`Map`，`subMap()`可以返回一个子树。 

### HashMap
&emsp;&emsp;底层实现：`HashMap`底层整体结构是一个数组，数组中的每个元素又是一个链表。每次添加一个对象（put）时会产生一个链表对象（`Object`类型），`Map`中的每个`Entry`就是数组中的一个元素（`Map.Entry`就是一个`<Key,Value>`），它具有由当前元素指向下一个元素的引用，这就构成了链表。
&emsp;&emsp;存储原理：当向`HsahMap`中添加元素的时候，先根据`HashCode`重新计算`Key`的`Hash`值，得到数组下标，如果数组该位置已经存在其他元素，那么这个位置的元素将会以链表的形式存放，新加入的放在链头，最先加入的放在链尾，如果数组该位置元素不存在，那么就直接将该元素放到此数组中的该位置。
&emsp;&emsp;去重原理：不同的`Key`算到数组下标相同的几率很小，新建一个`<K,V>`放入到`HashMap`的时候，首先会计算Key的数组下标，如果数组该位置已经存在其他元素，则比较两个Key，若相同则覆盖写入，若不同则形成链表。
&emsp;&emsp;读取原理：从`HashMap`中读取（get）元素时，首先计算`Key`的`HashCode`，找到数组下标，然后在对应位置的链表中找到需要的元素。
 &emsp;&emsp;扩容机制：当`HashMap`中的元素个数超过数组大小*`loadFactor`（默认值为0.75）时，就会进行2倍扩容（`oldThr << 1`）。

## 三者区别
|比较|    List    |       Set    |       Map        |
|:--:|:-----------:|:--------------:|:-------------------:|
|继承接口|    Collection  |    Collection   |           |
|常见实现类|AbstractList(其常用子类有ArrayList、LinkedList、Vector) | AbstractSet(其常用子类有HashSet、LinkedHashSet、TreeSet) |HashMap、HashTable |
|常见方法|add( )、remove( )、clear( )、get( )、contains( )、size( )|add( )、remove( )、clear( )、contains( )、size( )|put( )、get( )、remove( )、clear( )、containsKey( )、containsValue( )、keySet( )、values( )、size( )|
|元素|可重复|不可重复(用`equals()`判断)| 不可重复 |
|顺序|  有序  | 无序(实际上由HashCode决定) |      |
|线程安全|  Vector线程安全   |                  | Hashtable线程安全    |
