--- 
draft: false
date: 2024-07-08T16:19:01+08:00
title: "如何理解DAO，DTO，DO，VO，AO，BO，POJO，PO，Entity，Model，View？"
description: "如何理解DAO，DTO，DO，VO，AO，BO，POJO，PO，Entity，Model，View？"
slug: "24070816"
authors: ["howey"]
tags: ["java"]
categories: ["后端"]
externalLink: ""
series: ["编程系列"]
---
## 对应关系

|简称|描述|
|--|--|
|DAO |（Data Access Object）数据访问对象|
|DTO |（Data Transfer Object）数据传输对象|
|DO  |（Domain Object）领域对象|
|VO |（View Object）视图模型|
|AO |（Application Object）应用对象|
|BO |（ Business Object）业务对象|
|POJO |（Persistent Object）持久化对象|
|Entity |（应用程序域中的一个概念）实体|
|Model |（概念实体模型）实体类和模型|
|View  |（概念视图模型）视图模型|

## 概念

`DAO`(Data Access Object)是一个数据访问接口，数据访问：顾名思义就是与数据库打交道。夹在业务逻辑与数据库资源中间。

一般在 **业务逻辑层** （Service） 

对**数据库（SQL）** 的访问时使用，一般能对SQL进行操作。

xxxDAO，xxx即为实体类名（Entity实体）

其它资料：在核心J2EE模式中是这样介绍DAO模式的：为了建立一个健壮的J2EE应用，应该将所有对数据源的访问操作抽象封装在一个公共API中。用程序设计的语言来说，就是建立一个接口，接口中定义了此应用程序中将会用到的所有事务方法。在这个应用程序中，当需要和数据源进行交互的时候则使用这个接口，并且编写一个单独的类来实现这个接口在逻辑上对应这个特定的数据存储。

DAO是微软的第一个面向对象的数据库接口。DAO对象封闭了Access的Jet函数。通过Jet函数，它还可以访问其他的结构化查询语言（SQL）数据库。

### DTO（Data Transfer Object）数据传输对象

数据传输对象 DTO (Data Transfer Object)，是一种设计模式之间传输数据的软件应用系统。数据传输目标往往是数据访问对象从数据库中检索数据。数据传输对象与数据交互对象或数据访问对象之间的差异是一个以不具有任何行为除了存储和检索的数据（访问和存取器）。

一般在 前端（Web） 

对
 控制层（Controller）进行数据传输时使用，说白了就是

前端向后台
提交数据。

xxxDTO，xxx为业务领域相关的名称。

### DO （Domain Object）领域对象

领域对象 DO 是从现实世界中抽象出来的有形或无形的业务实体。在与数据有关的操作中数据存在数据库使用 DAO访问被取出来时，一般会将这些数据规范化的定义成类，而这个类就是DO，用来接收数据库对应的实体，它是一种抽象化的数据状态，介于数据库与业务逻辑之间。

一般在 业务逻辑层（Service） 

对
 数据库（SQL） 的 访问时 接收数据 使用。

xxxDO，xxx即为数据表名

另外
：DO与Entity概念上浅显的相同，他们在实际应用中是一个东西。稍微的不同点就是DO是与数据库存在着某种映射关系的Entity，总的来说DO是Entity的一种。

### VO（View Object）视图模型

VO是显示视图模型，视图对象，用于展示层，它的作用是把某个指定页面（或组件）的所有数据封装起来。如果是一个DTO对应一个VO，则DTO=VO;但是如果一个DTO对应多个VO，则展示层需要把VO转换为服务层对应方法所要求的DTO，传送给服务层。从而达到服务层与展示层解耦的效果。

一般用在业务逻辑层（Service） 

对
 前端（Web） 的 视图模型效果控制的展示上，说白了就是

后台向前端
传输数据。

xxxVO，xxx一般为网页名称。

### AO（Application Object）应用对象

AO是一个较为笼统的概念，因为太过于常见而并没有刻意的去描绘它的细节。举一个很简单的栗子：控制层（Controller） 在 业务逻辑层（Service） 查询一条或多条数据，这个数据的传输过程的运载就是AO完成。在正常的业务逻辑中一般都有很多种类型的数据，例如 整形、字符型、集合、类 等，我们把它统称为AO。

在**控制层（Controller）**与 **业务逻辑层（Service）**层之间抽象的复用对象模型，有时候极为贴近展示层，复用度不高。

### BO（ Business Object）业务对象

业务对象(Business Object，BO)是对数据进行检索和处理的组件。主要作用是把业务逻辑封装为一个对象。这个对象可以包括一个或多个其它的对象。形象描述为一个对象的形为和动作，当然也有涉及到基它对象的一些形为和动作。

一般用在包含业务功能模块 的具体实例上，比如我们写了一个Controller、一个Service、一个DAO、一个工具类等等这一系列实例组合后能实现一些功能，这些一系列实例组合为一个组件，这个组件就是BO。

其它资料：是简单的真实世界的软件抽象。业务对象通常位于中间层或者业务逻辑层。BO支持序列化和反序列化，可以轻易地将BO的Java实例转换为一个XML文件或者一个流保存起来，并且在需要的时候，将这个BO从XML或者流中转换回一个Java实例。

### POJO（ Plain Ordinary Java Object）纯普通Java对象

总的来说POJO包含DO、DTO、BO、VO，这些本质上都是一个简单的java对象，实际就是普通JavaBeans，是为了避免和EJB混淆所创造的简称

使用POJO名称是为了避免和EJB混淆起来, 而且简称比较直接.。其中有一些属性及其getter setter方法的类,没有业务逻辑，有时可以作为VO(value -object)或dto(Data Transform Object)来使用.当然，这里特意说明纯普通Java对象，如果你有一个简单的运算属性也是可以的,但不允许有业务方法,也不能携带有connection之类的方法。

### PO（Persistent Object）持久化对象

数据库表中的记录在java对象中的显示状态。最形象的理解就是一个PO就是数据库中的一条记录。
好处是可以把一条记录作为一个对象处理，可以方便的转为其它对象。

例如我们有一条数据，现在有一个简单类而且已经是被赋予了这条数据的实例，那么目前这条数据在这个简单类的存在状态就是PO，不管这个简单类是DO还是BO还是其他。PO只是数据持久化的一个状态。

### Entity（应用程序域中的一个概念）实体

ADO .NET Entity Framework 应用程序域中的一个概念，数据类型在该域中定义。

在计算机网络中，实体这一较为抽象的名词表示任何可能发送或接受信息的硬件或软件进程。在许多情况下，实体就是一个特定的软件模块。

说白了Eitity是一个未被持久化的对象，它是一个类，从现实抽象到代码的一个类。

### Model （概念实体模型）实体类和模型

Model是计算机程序设计中有两个概念：一个是三层架构中的实体类，另一个是MVC架构中的模型。

在“三层架构”中，为了面向对象编程，将各层传递的数据封装成实体类，便于数据传递和提高可读性。

在MVC（模型Model-视图View-控制器Controller）模式中，Model代表模型，是业务流程/状态的处理以及业务规则的制定，接受视图请求的数据，并返回最终的处理结果。业务模型的设计可以说是MVC最主要的核心。

### View （概念视图模型）视图模型

在MVC（模型Model-视图View-控制器Controller）模式中，View代表视图，用来解析Model带来的数据模型，以展示视图数据，View的模型觉决定了需要什么样的Model来对接，相互联系。
