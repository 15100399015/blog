### spring-aop
这个jar包含在应用中使用spring的AOP特性时所需的类和源码级元数据支持
使用基于AOP的Spring特性，如声明型事务管理，也要包含这个jar包



外部依赖

- spring-core

### spring-aspects
提供对Aspects的支持，以便可以方便的将面向切面的功能集成到IDE中，比如Eclipese AJDT

### spring-beans
这个jar文件是所有应用都要用到的，它包含访问配置文件，创建和管理bean以及进行 控制翻转和依赖注入，操作相关的所有类，如果应用只需要基本的Ioc/Di支持，引入spring-core 及 soring-beans 文件就可以了



外部依赖

- spring-core

### spring-context
这个jar文件为 Spring 核心提供了大量扩展，可以找到使用 Spring ApplicationContext 特性时所需的全部类，JDNI 所需的全部类，instrumentation组件以及校验Validation 方面的相关类。



外部依赖

- spring-beans

### spring-context-supper
包含支持缓存Cache（ehcache）,JCA,JMX,邮件服务，任务计划Scheduing 方面的类



外部依赖

- spring-context

### spring-core

这个jar文件包含Spring框架基本的核心工具类，Spring 其他组件都要使用到这个包里的类，是其他组件的核心，当然你也可以在自己的应用系统中使用这些工具类



外部依赖

- Commons Logging

### spring-expression

spring 表达式语言

### spring-instrument
spring3 对服务器的接口代理

### spring-instrument-tomcat

spring3.0 对tomcat的连接池的集成

### spring-jdbc

这个jar文件包含对Spring对JDBC数据访问进行封装的所有类



外部依赖

- spring-beans
- spring-dao

### spring-jms
这个jar包提供了对JMS的类的支持



外部依赖

- spring-beans
- spring-dao
- jms api

### spring-messaging

spring-messaging 模块为集成messaging api和消息协议提供支持

### spring-orm
包含spring对Dao特性集进行了扩展，使其支持 ibatis jdo ojb toplink 因为Hibernate已经独立成包了，现在不在这个包里，这个jar文件里大部分的类都要依赖spring-dao里的类，用这个包时需要同时包含spring-dao包

### spring-oxm
Spring 对Object/xml的映射支持，可以让java与xml之间来回切换

### spring-test
对junit等测试框架的简单封装

### spring-tx
以前是在这里 org.springframeword.transaction

为Jdbc Hibernate，jdo，jpa，beans 等提供一致的声明式，和编程式事务管理支持

### spring-web

这个jar文件包含了web应用开发时，用到spring框架时所需的核心类，包括自动载入 Web ApplicationContext 特性的类，Struts 与 JSF 集成类，文件上传的支持类，Filter类和大量工具辅助类



外部依赖 

- spring-context
- Servlet-api


### spring-webmvc
这个jar文件包含Spring-mvc 框架相关的所有类，包括框架的Servlets，WebMvc 框架，控制器和视图支持，当然，如果你的应用使用了独立的Mvc框架，则无需关系jar文件里的任何类



外部依赖

- spring-web

### spring-webmvc-protlet
### spring-websocket
### spring-dao
这个jar文件包含对Spring DAO，Spring Transaction 进行数据访问的所有类，为了使用声明式事务支持，还需在自己的应用里包含 spring-aop包



外部依赖

- spring-core

### spring-remoting

这个jar文件包含支持ejb，远程调用 Remoting 方面的类

### spring-portlet
spring自己实现了一个类似Spring-mvc的框架，包括一个MVC框架和控制器



外部依赖

- spring-web
- protlet api



