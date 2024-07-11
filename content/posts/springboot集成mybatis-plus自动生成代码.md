---
title: "springboot集成mybatis-plus自动生成代码"
date: 2024-07-11T14:21:16+08:00
draft: false
slug: "2407111421"
authors: ["howey"]
tags: ["mybatis","springBoot"]
series: ["编程系列"]
categories: ["后端"]
---
## 1. 引入依赖：`pom.xml`中添加
```xml
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.5.7</version>
        </dependency>
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.30</version>
        </dependency>
```
## 2. 在`src/test/java`目录下新建一个`Generator.java`文件`
```java
package com.nlkj.nlkjweb.AutoGenerateCode;



import com.baomidou.mybatisplus.generator.FastAutoGenerator;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.nio.file.Paths;

public class GeberatorServer {

    private static String dbUrl = "jdbc:mysql://xx.xx.xxx.xx:3306/nlkj_db?useSSL=false&serverTimezone=UTC";
    private static String dbUser = "xxxx";
    private static String dbPassword = "xxxx";
    private static String packageName = "com.nlkj.nlkjweb.admin";

    public static void main(String[] args) {
        FastAutoGenerator.create(dbUrl, dbUser, dbPassword)
                .globalConfig(builder -> builder
                        .author("howey")
                        .outputDir(Paths.get(System.getProperty("user.dir")) + "/src/main/java")
                        .commentDate("yyyy-MM-dd")
                )
                .packageConfig(builder -> builder
                        .parent(packageName)
                        .entity("entity")
                        .mapper("mapper")
                        .service("service")
                        .serviceImpl("service.impl")
                        .xml("mapper.xml")
                )
                .strategyConfig(builder -> builder
                        .addTablePrefix("nlkj_")
                        .entityBuilder()
                        .disableSerialVersionUID()
                        .enableLombok()
                        .enableTableFieldAnnotation()
                        .enableFileOverride()
                        .controllerBuilder()
                        .enableFileOverride()
                        .enableRestStyle()
                        .formatFileName("%sController")
                        .serviceBuilder()
                        .formatServiceFileName("%sService")
                        .formatServiceImplFileName("%sServiceImp")
                        .enableFileOverride()
                        .mapperBuilder()
                        .formatMapperFileName("%sMapper")
                        .formatXmlFileName("%sMapperXml")
                        .enableFileOverride()
                        .build()
                )
                .templateEngine(new FreemarkerTemplateEngine())
                .execute();
    }

}
```
## 3. 运行`Generator.java`文件，即可自动生成代码
以上代码生成后的文件结构如下：
```
├─main
│  ├─java
│  │  └─com
│  │      └─nlkj
│  │          └─nlkjweb
│  │              ├─admin
│  │              │  ├─controller
│  │              │  ├─entity
│  │              │  ├─mapper
│  │              │  │  └─xml
│  │              │  └─service
│  │              │      └─impl
│  │              ├─config
│  │              └─utils
│  └─resources
│      └─mapper
└─test
    └─java
        └─com
            └─nlkj
                └─nlkjweb
                    └─AutoGenerateCode
```
`注意`：要区分springboot3还是springboot2，然后选择对应不同的mybatis-plus版本