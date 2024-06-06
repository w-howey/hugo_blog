---
title: "springtBoot集成Knife4j"
date: 2024-06-06T11:21:16+08:00
draft: false
slug: "2406061121"
authors: ["howey"]
tags: ["java","Knife4j"]
series: ["编程系列"]
categories: ["后端"]
---
### 1. 在配置文件：`pom.xml`中加入
```xml
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>knife4j-openapi3-jakarta-spring-boot-starter</artifactId>
            <version>4.5.0</version>
        </dependency>
```
### 2. 在配置文件：`application.yml`中加入
```yml
# springdoc-openapi项目配置
springdoc:
  swagger-ui:
    path: /swagger-ui.html
    tags-sorter: alpha
    operations-sorter: alpha
  api-docs:
    path: /v3/api-docs
  group-configs:
    - group: 'default'
      paths-to-match: '/**'
      packages-to-scan: com.nlkj.nlkjweb.controller # 扫描的包，注意改成自己的包
# knife4j的增强配置，不需要增强可以不配
knife4j:
  enable: true
  setting:
    language: zh_cn
```
### 3. 新建config包，在config包中新建类：`SwaggerConfig`
```java
package com.nlkj.nlkjweb.config;

import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Contact;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import org.springdoc.core.models.GroupedOpenApi;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SwaggerConfig {
    @Bean
    public GroupedOpenApi adminApi() {      // 创建了一个api接口的分组
        return GroupedOpenApi.builder().group("admin-api")         // 分组名称
                .pathsToMatch("/**")  // 接口请求路径规则
                .build();
    }

    @Bean
    public OpenAPI openAPI() {
        return new OpenAPI().info(new Info() // 基本信息配置
                .title("fusApi") // 标题
                .description("Knife4j说明") // 描述Api接口文档的基本信息
                .version("v1") // 版本
                // 设置OpenAPI文档的联系信息，包括联系人姓名为"robin"，邮箱为"robin@gmail.com"。
                .contact(new Contact().name("howey").email("xxxxx@gmail.com"))
                // 设置OpenAPI文档的许可证信息，包括许可证名称为"Apache 2.0"，许可证URL为"http://springdoc.org"。
                .license(new License().name("Apache 2.0").url("http://springdoc.org")));
    }
}
```
### 4. 在controller中使用，例如在controller包中新建类：`TestController`
```java
package com.nlkj.nlkjweb.controller;

import io.swagger.v3.oas.annotations.Operation;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/admin")
public class TestController {
    @GetMapping("/hello")  // 请求路径
    @Operation(summary = "hello") // Api的描述
    public void testHello() {
        System.out.println("hello");
    }
}
```
### 5. 访问：`http://localhost:8080/doc.html`
### 6. 各注解参数如下
| 注解 | 描述|
|--|--|
| @OpenAPIDefinition	| 定义 OpenAPI 规范的基本信息，如 API 的标题、版本、服务器等|
| @Operation	| 用于描述 API 操作（端点），包括操作的摘要、描述、请求和响应等信息|
| @ApiResponse	| 定义操作的响应，包括响应的状态码、描述和响应模型等信息|
| @Parameter	| 定义操作的参数，包括路径参数、查询参数、请求头参数等|
| @PathVariable	| 定义路径参数，用于提取 URL 中的变量|
| @RequestParam	| 定义查询参数，用于从请求中获取参数的值|
| @ApiParam	| 在方法参数上使用，用于描述参数的含义和约束|
| @ApiResponses	| 在控制器类上使用，为多个操作定义通用的响应规范|
| @ApiResponseExtension	| 在 @Operation 或 @ApiResponse 上使用，用于扩展响应信息|
| @SecurityRequirement	| 定义操作所需的安全要求，如需要的身份验证方案、安全范围等|
| @SecurityScheme	| 定义安全方案，包括认证方式（如 Basic、OAuth2 等）、令牌 URL、授权 URL 等|
| @Tags	| 定义操作的标签，用于对操作进行分类和组织|
| @Hidden	| 在文档中隐藏标记的操作或参数，可以用于隐藏一些内部或不需要在文档中展示的部分|
| @Extension	| 用于为生成的 OpenAPI 文档添加自定义的扩展信息，可以在文档中增加额外的元数据或自定义字段|
| @RequestBodySchema	| 定义请求体的数据模型，允许对请求体进行更细粒度的描述和约束，如属性的名称、类型、格式、必填性等|
| @ApiResponseSchema	| 定义响应的数据模型，允许对响应体进行更细粒度的描述和约束，如属性的名称、类型、格式、必填性等|
| @ExtensionProperty	| 在 @Extension 注解上使用，用于定义自定义扩展的属性，可以添加额外的元数据或自定义字段到生成的 OpenAPI 文档中
### `详细见官网文档：https://doc.xiaominfo.com/`