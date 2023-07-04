# 1、 Spring基础

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

## 1.5 Spring中用到了哪些设计模式？

- **工厂模式**：Spring中通过BeanFactory、ApplicationContext创建Bean对象。
- **代理模式**：Spring AOP基于代理模式实现的。
- **单例模式**：Spring中的Bean默认都是单例的。
- **模板方法**：Spring 中 `jdbcTemplate`、`hibernateTemplate` 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。
- **包装器模式**：我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
- **观察者模式**：Spring 事件驱动模型就是观察者模式很经典的一个应用。
- **适配器模式**：Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配`Controller`。

# 2、Spring IoC

## 2.1 谈谈自己对Spring IoC的理解

**IoC（Inversion of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。不过， IoC 并非 Spring 特有，在其他语言中也有应用。

### 2.1.1 为什么叫控制反转？

- **控制** ：指的是**对象创建（实例化、管理）**的权力
- **反转** ：控制权交给外部环境（Spring 框架、IoC 容器）

![img](https://oss.javaguide.cn/java-guide-blog/frc-365faceb5697f04f31399937c059c162.png)

在 Spring 中， IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器**实际上就是个 Map（key，value）**，Map 中存放的是各种对象。

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

### 2.1.2 IoC有什么作用？

1. 管理对象的创建和依赖关系的维护。
2. 解耦，由容器去维护具体的对象。
3. 托管了类的整个生命周期。

## 2.2 什么是Spring Bean？

简单来说，Bean 代指的就是那些**被 IoC 容器所管理的对象**。

我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 XML 文件、注解或者 Java 配置类。

```xml
<!-- Constructor-arg with 'value' attribute -->
<bean id="..." class="...">
   <constructor-arg value="..."/>
</bean>
```

IoC容器如何使用配置元数据来管理对象：

![img](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/062b422bd7ac4d53afd28fb74b2bc94d.png)

## 2.3 将一个类声明为Bean的注解有哪些？

- `@Componet`：通用的注解，可以标注任意的类为`Spring`组件。如果不知道一个Bean属于哪个层，可以用`@Componet`注解来标注。
- `@Repository`：对应持久层，即Dao层，主要用于数据库的相关操作。
- `@Service`：对应服务层，主要涉及一些复杂的逻辑，需要用到Dao。
- `@Controller`：对应Spring MVC控制层，主要用于接受用户请求并调用`Service`层返回数据给前端页面。

## 2.4 @Componet和@Bean的区别是什么？

- `@Componet`注解作用于类，而`@Bean`注解作用于方法。
- `@Componet`通常是通过类路径扫描（可以用`@ComponetScan`定义扫描的路径）来自动侦测以及自动装配到Spring容器中。`@Bean`通常是我们在标有该注解的方法中定义产生这个bean，告诉Spring这是某个类的实例，当我需要用它的时候还给我。
- `@Bean`注解比`@Componet`注解的自定义性更强，而且很多地方只能通过`@Bean`来注册bean。比如当引用第三方库中的类，需要装配到Spring容器时，只能通过`@Bean`来实现。

`@Bean`注解的使用示例：

```java
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }
}
```

这段代码相当于下面的xml配置：

```xml
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

## 2.5 注入Bean的注解有哪些？

Spring内置的`@Autowired`（自动装配）以及JDK内置的`@Resource`和`@Inject`都可以注入Bean

### 2.5.1 使用@Autowired 注解自动装配 bean 的过程是怎样的?

在使用`@Autowired` 注解之前需要在 Spring 配置文件进行配置标签，然后在启动Spring IoC 时，容器就会自动装载一个`AutowiredAnnotationBeanPostProcessor`后置处理器，当容器扫描到`@Autowied`、`@Resource` 或`@Inject` 时，就会在 IoC 容器自动查找需要的 bean， 并装配给该对象的属性。

在使用`@Autowired` 时，首先在容器中查询对应类型的 Bean：

​	如果查询的结果为**空**，那么会抛出异常；

​	如果查询结果**刚好为一个**，就将该 Bean 装配给`@Autowired` 指定的属性； 

​	如果查询的结果**不止一个**，需要配合`@Qualifier` 注解根据名称来查找；

​		如果配合`@Qualifier` 注解根据名称来查找的结果为空，会抛出异常，可以将`@Autowire` 注解的 `required` 属性设置为 `false`。

## 2.6 @Autowired和@Resource的区别是什么？

`Autowired` 属于 Spring 内置的注解，**默认的注入方式为`byType`（根据类型进行匹配）**，也就是说会**优先根据接口类型去匹配并注入 Bean** （接口的实现类）。

**这会导致什么问题呢？** 

当一个接口存在多个实现类的话，`byType`这种方式就无法正确注入对象了，因为这个时候 Spring 会同时找到多个满足条件的选择，默认情况下它自己不知道选择哪一个。

这种情况下，**注入方式会变为 `byName`（根据名称进行匹配）**，这个名称通常就是类名（首字母小写）。就比如说下面代码中的 `smsService` 就是我这里所说的名称，这样应该比较好理解了吧。

```java
// smsService 就是我们上面所说的名称
@Autowired
private SmsService smsService;
```

举个例子，`SmsService` 接口有两个实现类: `SmsServiceImpl1`和 `SmsServiceImpl2`，且它们都已经被 Spring 容器所管理。

```java
// 报错，byName 和 byType 都无法匹配到 bean
@Autowired
private SmsService smsService;
// 正确注入 SmsServiceImpl1 对象对应的 bean
@Autowired
private SmsService smsServiceImpl1;
// 正确注入  SmsServiceImpl1 对象对应的 bean
// smsServiceImpl1 就是我们上面所说的名称
@Autowired
@Qualifier(value = "smsServiceImpl1")
private SmsService smsService;
```

所以，还是建议通过 `@Qualifier` 注解来显式指定名称而不是依赖变量的名称。

`@Resource`属于 JDK 提供的注解，**默认注入方式为 `byName`**。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为`byType`。

`@Resource` 有两个比较重要且日常开发常用的属性：`name`（名称）、`type`（类型）。

```java
public @interface Resource {
    String name() default "";
    Class<?> type() default Object.class;
}
```

如果仅指定 `name` 属性则注入方式为`byName`，如果仅指定`type`属性则注入方式为`byType`，如果同时指定`name` 和`type`属性（不建议这么做）则注入方式为`byType`+`byName`。

```java
// 报错，byName 和 byType 都无法匹配到 bean
@Resource
private SmsService smsService;
// 正确注入 SmsServiceImpl1 对象对应的 bean
@Resource
private SmsService smsServiceImpl1;
// 正确注入 SmsServiceImpl1 对象对应的 bean（比较推荐这种方式）
@Resource(name = "smsServiceImpl1")
private SmsService smsService;
```

---

总结，两者的区别：

- `@Autowired`是Spring提供的注解，而`@Resource`是JDK提供的注解；
- `@Autowired`默认注入方式为`byType`（根据类型匹配），`@Resource`默认注入方式为`byName`(根据名称匹配)；
- 当一个接口存在多个实现类的情况下，`@Autowired`和`@Resource`都需要通过名称才能正确匹配到对应的Bean。`@Autowired`可以通过`@Qualifier`**注解**来显式指定名称，`@Resource`可以通过`name`**属性**来显式指定名称。

## 2.7 Bean的作用域有哪些？

- **singleton** : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。

- **prototype** : 每次获取都会创建一个新的 bean 实例。也就是说，连续 `getBean()` 两次，得到的是不同的 Bean 实例。

- **request** （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean（请求 bean），该 bean 仅在当前 HTTP request 内有效。

- **session** （仅 Web 应用可用） : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。

- **application/global-session** （仅 Web 应用可用）： 每个 Web 应用在启动时创建一个 Bean（应用 Bean），该 bean 仅在当前应用启动时间内有效。

- **websocket** （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

### 2.7.1 如何配置Bean的作用域呢？

1. xml方式：

   ```java
   <bean id="..." class="..." scope="singleton"></bean>
   ```

2. 注解方式：

   ```java
   @Bean
   @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
   public Person personPrototype() {
       return new Person();
   }
   ```

## 2.8  单例Bean的线程安全问题了解吗？

单例 Bean 存在线程安全问题，主要是因为当**多个线程**操作**同一个对象**的时候是存在**资源竞争**的。

通常有两种解决方法：

1. 在Bean中尽量避免定义可以变的成员变量。
2. 在类中定义一个`ThreadLocal`成员变量，将需要的可变成员变量保存在`TheadLocal`中。（推荐）

不过，大部分Bean实际都是无状态（没有实际变量）的，比如说Dao、Service，这种情况下，Bean是线程安全的。

## 2.9 Bean的生命周期了解吗？

![image-20230619144343597](pictures\image-20230619144343597.png)

1. **通过BeanDefinition获取Bean的定义信息**，比如类的全路径，是否延迟加载，是否是单例等；
1. **调用构造函数实例化Bean** ；
1. **Bean的依赖注入**，比如set方法注入，平时用的`@Autowire`就是在这一步完成的；
1. **处理Aware接口**，比如BeanNameAware、BeanFactoryAware、ApplicationContextAware；
1. **Bean的后置处理器BeanPostProcessor-befor**；
1. **初始化方法**；
1. **Bean的后置处理器BeanPostProcessor-after**，主要就是对Bean进行增强；
1. **销毁Bean**；

## 2.10 Spring Bean中的循环依赖问题

循环依赖（循环引用）其实就是两个或两个以上的bean互相持有对方，最终形成闭环。比如A依赖B，B依赖A。

在Spring框架中根据三级缓存已经解决了大部分的循环依赖。

1. 一级缓存：单例池，缓存已经经历了完整的生命周期，已经初始化完成的bean对象；（解决不了循环依赖）
2. 二级缓存：缓存早期的bean对象（生命周期还没走完）；
3. 三级缓存：缓存的是ObjectFactory，表示对象工厂，用来创建某个对象的。

### 2.10.1 三级缓存具体是怎么解决循环依赖的呢？（具体流程是什么？）

<img src="pictures\image-20230619152949493.png" alt="image-20230619152949493" style="zoom:67%;" />

1. 先实例化A对象，同时会创建ObjectFactory对象存入三级缓存中；
2. A在初始化的时候需要B对象，这时候回去创建B对象；
3. B对象实例化完成，也会创建ObjectFactory对象存入三级缓存；
4. B对象需要注入A，可以通过三级缓存中的ObjectFactory对象生成A的半成品对象存入二级缓存；（此时的A对象可以是普通对象，也可以是代理对象）
5. B通过二级缓存获取到A的对象后，就可以正常注入A，然后B对象创建成功后，将对象B存入一级缓存；
6. 返回到A对象，因为此时B对象已经创建成功了，所以A对象可以直接注入B对象，完成对象A的创建，并存入一级缓存中；
7. 删除二级缓存中A的半成品对象；

### 2.10.2 构造方法出现了循环依赖怎么解决？

由于构造函数是在bean的生命周期中第一个执行的，三级缓存并不能解决构造函数的依赖注入问题。

所以**可以使用`@Lazy`懒加载注解解决**，什么时候需要对象，再进行bean对象的创建。

# 3、Spring AOP

## 3.1 谈谈自己对Spring AOP的理解

AOP(Aspect-Oriented Programming:面向切面编程)能够将那些**与业务无关，但却对多个对象产生影响的公共行为和逻辑**（例如事务处理、日志管理、权限控制等）抽取公共模块复用，便于**减少系统的重复代码**，**降低模块间的耦合度**，并有利于未来的**可拓展性**和**可维护性**。

Spring AOP 就是**基于动态代理**的，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理，如下图所示：

![SpringAOPProcess](https://oss.javaguide.cn/github/javaguide/system-design/framework/spring/230ae587a322d6e4d09510161987d346.jpeg)

### 3.1.1 常见的AOP使用场景

- 记录操作日志：使用AOP中的环绕通知+切点表达式来实现；
- 缓存处理
- Spring中内置的事务

## 3.2 Spring AOP和AspectJ AOP有什么区别？

**Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。**

 Spring AOP 基于**代理**(Proxying)，而 AspectJ 基于**字节码**操作(Bytecode Manipulation)。

如果的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比 Spring AOP 快很多。

## 3.3 JDK动态代理和Cglib动态代理的区别？

- JDK动态代理是**基于接口实现的代理**，使用**Java反射机制在运行时创建代理类**。JDK动态代理要求目标对象实现至少一个接口，代理类与目标类实现相同的接口，通过实现InvocationHandler接口并重写invoke()方法实现代理类的具体逻辑。

- Cglib动态代理是**基于继承实现的代理**，使用**字节码生成技术在运行时生成代理类**。Cglib动态代理不要求目标对象实现接口，可以**对任何类**进行代理。代理类继承目标类，通过重写目标类的方法实现代理类的具体逻辑。

相比于JDK动态代理，Cglib动态代理的**效率更高**，因为它不需要反射调用目标类的方法，而是通过直接调用代理类中重写的方法实现。但是Cglib动态代理也有一些限制，例如无法代理被final修饰的方法、类以及private、static等方法。

因此，**如果目标对象实现了接口，建议使用JDK动态代理；如果目标对象没有实现接口，或者需要代理被final修饰的方法，可以考虑使用Cglib动态代理**。

## 3.4 AOP中通知类型有哪些？

- **Before**（前置通知）：目标对象的方法调用之前触发

- **After** （后置通知）：目标对象的方法调用之后触发

- **AfterReturning**（返回通知）：目标对象的方法调用完成，在返回结果值之后触发

- **AfterThrowing**（异常通知） ：目标对象的方法运行中抛出 / 触发异常后触发。AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。

- **Around** （环绕通知）：编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法

## 3.5 多个切面的执行顺序如何控制？

1、通常使用`@Order` 注解直接定义切面顺序

```java
// 值越小优先级越高
@Order(3)
@Component
@Aspect
public class LoggingAspect implements Ordered {
```

**2、实现`Ordered` 接口重写 `getOrder` 方法。**

```java
@Component
@Aspect
public class LoggingAspect implements Ordered {
    // ....
    @Override
    public int getOrder() {
        // 返回值越小优先级越高
        return 1;
    }
}
```

# 4、Spring MVC

## 4.1 谈谈自己对Spring MVC的理解

MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其**核心思想**是通过将**业务逻辑、数据、显示**分离来组织代码。

![img](https://oss.javaguide.cn/java-guide-blog/image-20210809181452421.png)

Spring MVC 模式下我们一般把后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层(控制层，返回数据给前台页面)。

### 4.1.1 Spring MVC的优点

1. 可以支持各种视图技术，而不仅仅局限于 JSP；
2. 与 Spring 框架集成（如 IoC 容器、AOP 等）；
3. 清晰的角色分配：前端控制器(DispatcherServlet) , 处理器映射器（HandlerMapping)，处理器适配器（HandlerAdapter)，视图解析器（ViewResolver）。
4. 支持各种请求资源的映射策略。

## 4.2 Spring MVC的核心组件有哪些？

- **`DispatcherServlet`** ：**前端控制器**，负责接收请求、分发，并给予客户端响应。

- **`HandlerMapping`** ：**处理器映射器**，根据 uri 去匹配查找能处理的 `Handler` ，并会将请求涉及到的拦截器和 `Handler` 一起封装。

- **`HandlerAdapter`** ：**处理器适配器**，根据 `HandlerMapping` 找到的 `Handler` ，适配执行对应的 `Handler`；

- **`Handler`** ：**请求处理器**，处理实际请求的处理器。

- **`ViewResolver`** ：**视图解析器**，根据 `Handler` 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 `DispatcherServlet` 响应客户端

## 4.3 Spring MVC的工作原理？

Spring MVC的工作原理如下图：

![image-20230619160237862](pictures\image-20230619160237862.png)

1. 客户端发送请求，`DispatcherServlet`拦截请求；
2. `DispatcherServlet`根据请求信息调用`HandlerMapping`。`HandlerMapping`根据url去匹配查找能处理`Handler`（也就是常说的`Controller`控制器），并将请求涉及到的拦截器和`Handler`一起封装。
3. `DispatcherServlet`调用`HandlerAdapter`适配执行`Handler`。
4. `Handler`完成对用户请求的处理后，会返回一个`ModelAndView`对象给`DispatcherServlet`。`ModelAndView` 顾名思义，包含了数据模型以及相应的视图的信息。`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`。
5. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`。
6. `DispatcherServlet`会把返回的`Model`传给`View`（视图渲染）。
7. 最后再把`View`返回给客户端。

但是现在的开发过程基本都是接口开发、前后端分离，采用json格式传递数据，并没有ModelAndView，所以现在的处理流程会简单很多：

![image-20230619160509569](pictures\image-20230619160509569.png)

具体的步骤前半部分与之前一样，只是在controller上加个@ResponseBody注解，将结果转换为JSON并响应，并没有视图那一步。

## 4.4 统一异常处理怎么做？

推荐使用注解的方式统一异常处理，具体会使用到 `@ControllerAdvice` + `@ExceptionHandler` 这两个注解 。

```java
@ControllerAdvice
@ResponseBody
public class GlobalExceptionHandler {

    @ExceptionHandler(BaseException.class)
    public ResponseEntity<?> handleAppException(BaseException ex, HttpServletRequest request) {
      //......
    }

    @ExceptionHandler(value = ResourceNotFoundException.class)
    public ResponseEntity<ErrorReponse> handleResourceNotFoundException(ResourceNotFoundException ex, HttpServletRequest request) {
      //......
    }
}
```

这种异常处理方式下，会给所有或者指定的 `Controller` 织入异常处理的逻辑（AOP），当 `Controller` 中的方法抛出异常的时候，由被`@ExceptionHandler` 注解修饰的方法进行处理。

`ExceptionHandlerMethodResolver` 中 `getMappedMethod` 方法决定了异常具体被哪个 `@ExceptionHandler` 注解修饰的方法处理异常。

```java
@Nullable
private Method getMappedMethod(Class<? extends Throwable> exceptionType) {
    List<Class<? extends Throwable>> matches = new ArrayList<>();
    //找到可以处理的所有异常信息。mappedMethods 中存放了异常和处理异常的方法的对应关系
    for (Class<? extends Throwable> mappedException : this.mappedMethods.keySet()) {
        if (mappedException.isAssignableFrom(exceptionType)) {
            matches.add(mappedException);
        }
    }
    // 不为空说明有方法处理异常
    if (!matches.isEmpty()) {
        // 按照匹配程度从小到大排序
        matches.sort(new ExceptionDepthComparator(exceptionType));
        // 返回处理异常的方法
        return this.mappedMethods.get(matches.get(0));
    }
    else {
        return null;
    }
}
```

从源代码看出： **`getMappedMethod()`会首先找到可以匹配处理异常的所有方法信息，然后对其进行从小到大的排序，最后取最小的那一个匹配的方法(即匹配度最高的那个)。**

# 5、Spring事务

## 5.1 Spring管理事务的方法有几种？

- **编程式事务**。在代码中硬编码（不推荐）：通过 `TransactionTemplate`或者 `TransactionManager` 手动管理事务，实际应用中很少使用，但是对于理解 Spring 事务管理原理有帮助。
- **声明式事务**。在 XML 配置文件中配置或者直接基于注解（推荐） : 实际是通过 AOP 实现（基于`@Transactional` 的全注解方式使用最多）

## 5.2 Spring事务中有哪几种事务传播行为？

Spring 事务的传播行为是指：当多个事务同时存在的时候，Spring如何处理这些事务的行为。**为了解决业务层方法之间互相调用的事务问题**。

1. **`PROPAGATION_REQUIRED`**：使用的最多的一个事务传播行为，我们平时经常使用的`@Transactional`注解默认使用就是这个事务传播行为。如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
2. **`PROPAGATION_REQUIRES_NEW`**：创建一个新的事务，如果当前存在事务，则把当前事务挂起。也就是说不管外部方法是否开启事务，`Propagation.REQUIRES_NEW`修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。
3. **`PROPAGATION_NESTED`**：如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于`PROPAGATION_REQUIRED`。
4. **`PROPAGATION_MANDATORY`**：如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性），这个使用的很少。

若是错误的配置以下 3 种事务传播行为，事务将不会发生回滚：

- **`PROPAGATION_SUPPORTS`**: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。

- **`PROPAGATION_NOT_SUPPORTED`**: 以**非事务**方式运行，如果当前存在事务，则**把当前事务挂起**。

- **`PROPAGATION_NEVER`**: 以**非事务**方式运行，如果当前存在事务，则**抛出异常**。

## 5.3 Spring事务中的隔离级别有哪几种？

- **`ISOLATION_DEFAULT`** :使用后端数据库默认的隔离级别，MySQL 默认采用的 `REPEATABLE_READ` 隔离级别 ，Oracle 默认采用的 `READ_COMMITTED` 隔离级别.

- **`ISOLATION_READ_UNCOMMITTED`** :最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**

- **`ISOLATION_READ_COMMITTED`** : 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**

- **`ISOLATION_REPEATABLE_READ`** : 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生。**

- **`ISOLATION_SERIALIZABLE`** : 最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

## 5.4 Spring事务中失效的场景有哪些？

- 异常捕获处理，自己处理了异常，没有抛出，手动抛出就行；
- 抛出检查异常，配置rollbackFor属性为Exception；
- 非public方法，改为public；

# 6、Spring Boot

## 6.1 什么是Spring Boot？

Spring Boot 是 Spring 源组织下的子项目，是 Spring 组件一站式解决方案，主要是简化了使用 Spring 的难度，**简化了繁重的配置**，**提供了各种启动器，开发者能快速上手**。

## 6.2 Spring Boot的优点？

1. 容易上手，提升开发效率，为 Spring 开发提供一个更快、更广泛的入门体验。
2. 开箱即用，远离繁琐的配置。
3. 没有代码生成，也不需要 XML 配置。
4. 提供了一系列大型项目通用的非业务性功能，例如：内嵌服务器、安全管理、运行数据监控、运行状况检查和外部化配置等。
5. 避免大量的 Maven 导入和各种版本冲突。

## 6.3 谈谈Spring Boot自动装配原理 ？

在 SpringBoot 应用的每个启动类上都会有个注解`@SpringBootApplication`

```java
@SpringBootApplication
public class SpringSecurityJwtGuideApplication {
      public static void main(java.lang.String[] args) {
        SpringApplication.run(SpringSecurityJwtGuideApplication.class, args);
    }
}
```

我们可以把 `@SpringBootApplication`看作是 `@SpringBootConfiguration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解的集合。

根据 SpringBoot 官网，这三个注解的作用分别是：

- `@EnableAutoConfiguration`：开启 SpringBoot 的自动配置机制
- `@ComponentScan`： 扫描被`@Component` (`@Repository`,`@Service`,`@Controller`)注解的 bean，注解默认会扫描该类所在的包下所有的类。
- `@SpringBootConfiguration`：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类

其中`@EnableAutoConfiguration`注解是实现自动配置的核心注解。该注解**通过`@Import`注解导入对应的配置选择器**，关键的是内部读取了该项目和该项目引用的jar包的classpath路径下**META-INF/spring.factories**文件中**所配置的类的全类名**。

这些配置类中所定义的Bean会**根据条件注解所指定的条件**来决定是否要将其导入到Spring容器中。

比如`@ConditionalOnClass`注解，判断是否有对应的class文件，如果有则加载该类，把这个配置类的所有Bean放入到Spring中使用。

---

具体来说SpringBoot的自动装配是通过注解实现的，当满足某些条件时，自动装配相应的组件。

1. 判断自动装配开关是否打开。默认`spring.boot.enableautoconfiguration=true`，可在 `application.properties` 或 `application.yml` 中设置；

2. 用于获取`EnableAutoConfiguration`注解中的 `exclude` 和 `excludeName`；

3. 获取需要自动装配的所有配置类，读取`META-INF/spring.factories`；

4. 到这里可能面试官会问你:“`spring.factories`中这么多配置，每次启动都要全部加载么？”。

   **很明显，这是不会的**。因为，这一步有经历了一遍筛选，`@ConditionalOnXXX` 中的所有条件都满足，该类才会生效。

值得注意的是，Spring Boot的自动装配仅限于Spring框架本身提供的组件和第三方库中的Spring组件，对于其他的组件，需要手动进行配置。

## 6.4 Spring Boot Starter是什么？如何自定义？

Spring Boot Starter 是 Spring boot 的核心，可以理解为一个可拔插式的插件。

例如，想使用Reids插件，那么可以导入spring-boot-starter-redis 依赖 Starter 的命名。官方对 Starter 项目的 jar 包定义的 artifactId 是有要求的 ， 当然也可以不遵守 。

 Spring 官 方 Starter 通 常 命 名 为 `spring-boot-starter-{name}`如：`spring-boot-starter-web`，Spring 官方建议非官方的 starter 命名应遵守`{name}-spring-boot-starter` 的格式。

---

自定义starter的步骤如下：

1. 新建一个maven项目，在pom.xml文件中定义好所需要的依赖；
2. 新建配置类，写好配置项和默认值，使用`@ConfigurationProperties`指明配置前缀；
3. 新建自动装配类，使用`@Configuration`和`@Bean`进行自动装配；
4. 新建Spring.factories文件，用于指定自动装配类的路径；
5. 将starter安装到maven仓库，让其他项目能够引用。

## 6.5 Spring Boot核心配置文件

Spring boot 核 心 的 两个配置文件 ：

1. ` bootstrap` (. yml 或 者 . properties)：bootstrap 由父 ApplicationContext 加载的，比 applicaton 优先加载，配置在应用程序上下文的引导阶段生效。一般来说我们在 Spring Cloud Config 或者 Nacos 中会用到它。且 bootstrap 里面的属性不能被覆盖； 
2. `application `(. yml 或者 . properties)：由 ApplicatonContext 加 载，用于 Spring boot 项目的自动化配置

## 6.6 Spring Boot打成jar和普通jar有什么区别？

Spring Boot 打成的 jar **无法被其他项目依赖**，主要还是他和普通 jar 的结构不同。

普通的 jar 包，解压后直接就是包名，包里就是我们的代码，而 Spring Boot 打包成的可执行 jar 解压后，在\BOOT-INF\classes 目录下才是我们的代码，因此无法被直接引用。如果非要引用，可以在 pom.xml 文件中增加配置， 将 Spring Boot 项目打包成两个 jar ，一个可执行，一个可引用

## 6.7 Spring Boot和Spring Cloud的区别？

SpringBoot 专注于快速、方便的开发**单个微服务个体**，SpringCloud 关注**全局的服务治理框架**。

SpringCloud 将 SpringBoot 开发的一个个单体微服务整合并管理起来，为各个微服务之间提供配置管理、服务发现、断路器、路由、 微代理、事件总线、全局锁、决策竞选、分布式会话等等集成服务。

SpringBoot 可以离开SpringCloud 独立开发项目 ， 但是 SpringCloud 离不开 SpringBoot ，**属于依赖的关系**。
