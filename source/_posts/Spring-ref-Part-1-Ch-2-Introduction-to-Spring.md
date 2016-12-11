---
title: 'Spring-ref Part 1 Ch 2: Introduction to Spring'
date: 2016-07-27 17:06:41
tags: spring-ref
---

### Introduction
Spring让你能够基于Java平台开发应用。Spring能够使用POJO开发应用并且无侵入地把企业级服务应用到POJO上。
Spring兼容Java SE和部分Java EE。
### 2.1 依赖注入和控制反转
应用中的对象都会彼此依赖。尽管Java平台可以按功能开发，但是缺乏组织功能单元的方法，留给了架构师和开发者。尽管你可以使用一些设计模式，你也要在应用中实现最适合自己的模式。
Spring IOC组件把应用中独立的功能模块组织起来。Spring把设计模式作为第一类对象，你可以集成到自己的应用中。
### 2.2 模块
Spring由20个模块(Jars)构成，可以分组为Core Container, Data Access/Integration, Web, AOP, Instrumentation, Messaging和Test。 
![Overview of the Spring Framework](http://img.kerolgao.com/spring-overview.png)
#### Core Container
- spring-core
Core utilities, used by many other Spring modules
- spring-beans
Beans support, including Groovy
- spring-context
Application context runtime, including scheduling and remoting abstractions
- spring-context-support
Support classes for integrating common third-party libraries into a Spring application context
- spring-expression
Spring Expression Language (SpEL)

核心容器由spring-core, spring-beans, spring-context, spring-context-support和spring-expression组成。
spring-core and spring-beans提供框架基本功能特点，包括Ioc和DI。BeanFactory是工厂模式更为复杂的实现，把依赖的配置从编程逻辑中解耦出来。
spring-context建立在spring-core and spring-beans。Context模块继承了Beans模块的特点，添加了对国际化、事件传递、资源加载等。ApplicationContext接口把Context模块集中在一起。sprign-context-support支持集成一些第三方库到Spring应用中，如caching(EhCache, JCache), mailing(JavaMail), scheduling(CommonJ, Quartz) and template engines(FreeMarker).  

#### AOP and Instrumentation
- spring-aop
  Proxy-based AOP support
- spring-aspects
  AspectJ based aspects
- spring-instrument
  Instrumentation agent for JVM bootstrapping
- spring-instrument-tomcat
  Instrumentation agent for Tomcat

#### Messaging
- spring-messaging
Support for messaging architectures and protocols

#### Data Access/Integration
- spring-jdbc
JDBC support package, including DataSource setup and JDBC access support
- spring-orm
Object/Relational Mapping, including JPA and Hibernate support
- spring-oxm
Object/XML Mapping
- spring-jms
MS support package, including helper classes to send and receive JMS messages
- spring-tx
Transaction infrastructure, including DAO support and JCA integration

#### Web
- spring-web
Web support packages, including client and web remoting
- spring-webmvc
REST Web Services and model-view-controller implementation for web applications
- spring-webmvc-portlet
MVC implementation to be used in a Portlet environment
- spring-websocket
WebSocket and SockJS implementations, including STOMP support

Web层组成：spring-web, spring-webmvc, spring-websocket, spring-webmvc-portlet.  
spring-web提供了基本的web开发功能，如多部分文件上传、使用servlet监听器初始化IoC容器、web应用上下文等。它也包含了http客户端和远程调用的相关功能。  
spring-webmvc模块包含MVC框架及REST服务。  

#### Test
- spring-test
Support for unit testing and integration testing Spring components

