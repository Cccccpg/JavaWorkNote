# 1. Spring基础

## 1.1 什么是Spring框架？

Spring 是一款开源的**轻量级 Java 开发框架**，旨在提高开发人员的**开发效率**以及**系统的可维护性**。

Spring 最核心的思想就是不重新造轮子，开箱即用，提高开发效率。

## 1.2 Spring的模块有哪些？

以下是Spring5.x版本的模块结构图：

![Spring5.x主要模块](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/20200831175708.png)

Spring4.x 版本中 Web 模块的 Portlet 组件已经被废弃掉，同时增加了用于异步响应式处理的 WebFlux 组件。

### 1.2.1 Core Container模块

这是Spring的核心模块，主要提供IoC依赖注入功能的支持。

- **spring-core** ：Spring 框架基本的核心工具类。

- **spring-beans** ：提供对 bean 的创建、配置和管理等功能的支持。

- **spring-context** ：提供对国际化、事件传播、资源加载等功能的支持。

- **spring-expression** ：提供对表达式语言（Spring Expression Language） SpEL 的支持，**只依赖于 core 模块**，不依赖于其他模块，可以单独使用

### 1.2.2 AOP

- **spring-aspects** ：该模块为与 AspectJ 的集成提供支持。

- **spring-aop** ：提供了面向切面的编程实现。

- **spring-instrument** ：提供了为 JVM 添加代理（agent）的功能。 具体来讲，它为 Tomcat 提供了一个织入代理，能够为 Tomcat 传递类文件，就像这些文件是被类加载器加载的一样。没有理解也没关系，这个模块的使用场景非常有限。

### 1.2.3 Data Access/Integration

- **spring-jdbc** ：提供了对数据库访问的抽象 JDBC。不同的数据库都有自己独立的 API 用于操作数据库，而 Java 程序只需要和 JDBC API 交互，这样就屏蔽了数据库的影响。

- **spring-tx** ：提供对事务的支持。

- **spring-orm** ： 提供对 Hibernate、JPA 、iBatis 等 ORM 框架的支持。

- **spring-oxm** ：提供一个抽象层支撑 OXM(Object-to-XML-Mapping)，例如：JAXB、Castor、XMLBeans、JiBX 和 XStream 等。

- **spring-jms** : 消息服务。自 Spring Framework 4.1 以后，它还提供了对 spring-messaging 模块的继承。

### 1.2.4 Spring Web

- **spring-web** ：对 Web 功能的实现提供一些最基础的支持。

- **spring-webmvc** ： 提供对 Spring MVC 的实现。

- **spring-websocket** ： 提供了对 WebSocket 的支持，WebSocket 可以让**客户端和服务端进行双向通信**。

- **spring-webflux** ：提供对 WebFlux 的支持。WebFlux 是 Spring Framework 5.0 中引入的新的响应式框架。与 Spring MVC 不同，它不需要 Servlet API，是完全异步。

### 1.2.5 Messaging

**spring-messaging** 是从 Spring4.0 开始新加入的一个模块，主要职责是为 Spring 框架集成一些基础的报文传送应用。

### 1.2.6 Spring Test

Spring 团队提倡测试驱动开发（TDD）。有了控制反转 (IoC)的帮助，单元测试和集成测试变得更简单。

Spring 的测试模块对 JUnit（单元测试框架）、TestNG（类似 JUnit）、Mockito（主要用来 Mock 对象）、PowerMock（解决 Mockito 的问题比如无法模拟 final, static， private 方法）等等常用的测试框架支持的都比较好。

## 1.3 Spring、SpringMVC、SpringBoot之间有什么关系？

Spring 是一个轻量级 Java 开源框架，目的是解决企业级应用开发的复杂性，简化 Java 开发。Spring 通过一个 IoC 容器，来管理 Bean 对象，使用依赖注入实现控制反转，可以很方便的整合各种框架，提供 AOP 机制弥补 OOP 的代码重复问题、更方便将不同类不同方法中的共同处理抽取成切面、自动注入给方法执行，比如日志、异常等。

Spring MVC 是 Spring 中的一个很重要的模块，主要赋予 Spring 快速构建 MVC 架构的 Web 程序的能力。MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，**其核心思想是通过将业务逻辑、数据、显示分离来组织代码**。

![img](https://oss.javaguide.cn/java-guide-blog/image-20210809181452421.png)

Spring Boot 旨在简化 Spring 开发，简化了配置，如果你需要构建 MVC 架构的 Web 程序，你还是需要使用 Spring MVC 作为 MVC 框架，只是说 Spring Boot 帮你简化了 Spring MVC 的很多配置，真正做到开箱即用！

## 1.4 Spring的优缺点是什么？

### 1.4.1 优点

1. **轻量级**：Spring 框架不需要使用繁重的 EJB 组件，可以在轻量级容器中运行，因此开发和部署更为简单。
2. **面向切面编程**（AOP）：Spring 提供了很好的 AOP 支持，使得开发者可以更加容易地实现事务管理、安全、日志等功能，降低了代码的复杂度。
3. **控制反转**（IOC）：Spring 提供了依赖注入（DI）的支持，通过容器管理对象之间的依赖关系，降低了耦合度，使得代码更加可维护。
4. **支持多种开发框架**：Spring 对各种开发框架（如 Struts、Hibernate、MyBatis 等）都提供了很好的支持，使得开发者可以更加容易地集成这些框架。
5. **提高开发效率**：Spring 提供了很多实用的工具和模板，如 JDBC 模板、ORM 模板等，使得开发者可以更加快速地开发出高质量的应用。

### 1.4.2 缺点

1. **学习曲线较陡峭**：Spring 框架是一个比较复杂的框架，需要开发者学习很多概念和 API，因此学习曲线较陡峭。
2. **过度封装**：Spring 提供了很多封装，使得开发者很难理解其中的原理，也使得一些简单的操作变得复杂。
3. **运行效率**：Spring 框架由于需要进行大量的依赖注入和 AOP 操作，因此在运行时会消耗一定的系统资源，可能会影响应用的运行效率。
4. **容器过重**：Spring 的容器较重，启动速度较慢，可能会对应用的性能造成一定的影响。

# 2、Spring IoC

## 2.1 谈谈自己对Spring IoC的理解

**IoC（Inversion of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。不过， IoC 并非 Spring 特有，在其他语言中也有应用。

**为什么叫控制反转？**

- **控制** ：指的是对象创建（实例化、管理）的权力
- **反转** ：控制权交给外部环境（Spring 框架、IoC 容器）

![img](https://oss.javaguide.cn/java-guide-blog/frc-365faceb5697f04f31399937c059c162.png)

