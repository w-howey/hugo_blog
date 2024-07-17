---
title: "springboot中application.xml多环境配置"
date: 2024-07-11T15:09:16+08:00
draft: false
slug: "2407111509"
authors: ["howey"]
tags: ["springBoot"]
series: ["编程系列"]
categories: ["后端"]
---
## 1. 新建`application-dev.xml`和`application-prod.xml`文件
## 2. 在`application.xml`文件中配置`spring.profiles.active`属性`
```yml
spring:
  profiles:
    active: dev
```
## 3. 在`application-dev.xml`文件中配置开发环境需要的属性
