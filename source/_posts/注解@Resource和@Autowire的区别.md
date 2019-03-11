---
title: 注解@Resource和@Autowire的区别
categories: 
  - Java
tags:
  - Java
  - Spring Boot
date: 2018-03-08 10:36:53
---

> 平时在项目中用@Resource和@Autowired都能达到自动注入的效果，可是他们有什么不同呢？

<!-- more -->
# @Resource和@Autowired

- `@Resource`和`@Autowired`都可以用来装配bean，都可以用于字段或setter方法。
- `@Autowired`默认**按类型**装配，默认情况下必须要求依赖对象必须存在，如果要允许null值，可以设置它的required属性为false。
- `@Resource`默认**按名称**装配，当找不到与名称匹配的bean时才按照类型进行装配。名称可以通过name属性指定，如果没有指定name属性，当注解写在字段上时，默认取字段名，当注解写在setter方法上时，默认取属性名进行装配。
> 注意：如果name属性一旦指定，就只会按照名称进行装配。


- `@Autowire`和`@Qualifier`配合使用效果和`@Resource`一样：
  ```java
  @Autowired(required = false) @Qualifier("example")
  private Example example;
  
  @Resource(name = "example")
  private Example example;
  ```

- `@Resource`装配顺序
	1. 如果同时指定name和type，则从容器中查找唯一匹配的bean装配，找不到则抛出异常
	2. 如果指定name属性，则从容器中查找名称匹配的bean装配，找不到则抛出异常
	3. 如果指定type属性，则从容器中查找类型唯一匹配的bean装配，找不到或者找到多个抛出异常
	4. 如果都不指定，则自动按照byName方式装配，如果没有匹配，则回退一个原始类型进行匹配，如果匹配则自动装配

### 简要对比表格
|   注解对比   | `@Resource` | `@Autowire`  |
| :---------: | :----------:| :----------: |
|   注解来源   |     JDK     |    Spring    |
|   装配方式   |  优先按名称   |   优先按类型   |
|     属性     |  name、type |   required   |
