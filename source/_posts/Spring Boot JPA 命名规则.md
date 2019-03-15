---
title: Spring Boot JPA命名规则
categories: 
  - Spring Boot
tags:
  - Java
  - Spring Boot
  - 微服务
date: 2018-01-12 10:20:53
---

> Spring Boot 中JPA的使用真方便，那么它的命名要遵循哪些规则呢？

<!-- more -->

以下表格摘录自[官方文档](https://docs.spring.io/spring-data/jpa/docs/1.4.3.RELEASE/reference/html/jpa.repositories.html)

| Keyword | Sample	| JPQL snippet |
| :-------: | :-------: | :----------:  |
| And	| findByLastnameAndFirstname	| … where x.lastname = ?1 and x.firstname = ?2 |
| Or	| findByLastnameOrFirstname| 	… where x.lastname = ?1 or x.firstname = ?2 |
| Between	| findByStartDateBetween	| … where x.startDate between 1? and ?2 |
| LessThan	| findByAgeLessThan| 	… where x.age < ?1 |
| GreaterThan	| findByAgeGreaterThan	| … where x.age > ?1 |
| After	| findByStartDateAfter	| … where x.startDate > ?1 |
| Before	| findByStartDateBefore	| … where x.startDate < ?1 |
| IsNull	| findByAgeIsNull	| … where x.age is null |
| IsNotNull,NotNull	| findByAge(Is)NotNull	| … where x.age not null |
| Like	| findByFirstnameLike	| … where x.firstname like ?1 |
| NotLike	| findByFirstnameNotLike	| … where x.firstname not like ?1 |
| StartingWith| 	findByFirstnameStartingWith	| … where x.firstname like ?1 (parameter bound with appended %) |
| EndingWith	| findByFirstnameEndingWith	| … where x.firstname like ?1 (parameter bound with prepended %) |
| Containing	| findByFirstnameContaining	| … where x.firstname like ?1 (parameter bound wrapped in %) |
| OrderBy	| findByAgeOrderByLastnameDesc	| … where x.age = ?1 order by x.lastname desc |
| Not	| findByLastnameNot	| … where x.lastname <> ?1 |
| In	| findByAgeIn(Collection<Age> ages)	| … where x.age in ?1 |
| NotIn| 	findByAgeNotIn(Collection<Age> age) | 	… where x.age not in ?1 |
| True	| findByActiveTrue()	| … where x.active = true |
| False	| findByActiveFalse()	| … where x.active = false |
