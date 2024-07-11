---
title: "springboot集成mybatis-plus-generator-ui自动生成代码"
date: 2024-07-11T14:45:16+08:00
draft: false
slug: "2407111445"
authors: ["howey"]
tags: ["mybatis-plus","springBoot"]
series: ["编程系列"]
categories: ["后端"]
---
## 1. 引入依赖：`pom.xml`中添加
```xml
<dependency>
    <groupId>com.github.davidfantasy</groupId>
    <artifactId>mybatis-plus-generator-ui</artifactId>
    <version>2.0.5</version>
    <scope>test</scope>
</dependency>
```
## 2. 在`src/test/java`目录下新建一个`Generator.java`文件`
```java
public class GeberatorUIServer {

    public static void main(String[] args) {
        GeneratorConfig config = GeneratorConfig.builder().jdbcUrl("jdbc:mysql://192.168.1.211:3306/example")
                .userName("root")
                .password("root")
                .driverClassName("com.mysql.cj.jdbc.Driver")
                //数据库schema，MSSQL,PGSQL,ORACLE,DB2类型的数据库需要指定
                .schemaName("myBusiness")
                //数据库表前缀，生成entity名称时会去掉(v2.0.3新增)
                .tablePrefix("t_")
                //如果需要修改entity及其属性的命名规则，以及自定义各类生成文件的命名规则，可自定义一个NameConverter实例，覆盖相应的名称转换方法，详细可查看该接口的说明：                
                .nameConverter(new NameConverter() {
                    /**
                     * 自定义Service类文件的名称规则，entityName是NameConverter.entityNameConvert处理表名后的返回结果，如有特别的需求可以自定义实现
                     */
                    @Override
                    public String serviceNameConvert(String entityName) {
                        return entityName + "Service";
                    }

                    /**
                     * 自定义Controller类文件的名称规则
                     */
                    @Override
                    public String controllerNameConvert(String entityName) {
                        return entityName + "Action";
                    }
                })
                //所有生成的java文件的父包名，后续也可单独在界面上设置
                .basePackage("com.github.davidfantasy.mybatisplustools.example")
                .port(8068)
                .build();
        MybatisPlusToolsApplication.run(config);
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
## 4.github地址：`https://github.com/davidfantasy/mybatis-plus-generator-ui?tab=readme-ov-file`