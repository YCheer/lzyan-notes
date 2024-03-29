![](https://resource.lzyan.fun/PigGo/20220702151039.png)

## 概述

### 什么是 Spring 框架？

`Spring` 是一款开源的轻量级 `Java` 开发框架，旨在提高开发人员的开发效率以及系统的可维护性。一般说 Spring 框架指的都是 `Spring Framework`，它是很多模块的集合，由于 `Spring Frameworks` 的分层架构，用户可以自由选择自己需要的组件，使用这些模块可以很方便地协助开发。

比如说 `Spring` 自带 `IoC（Inverse of Control:控制反转）` 和 `AOP(Aspect-Oriented Programming:面向切面编程)`，可以很方便地对数据库进行访问、可以很方便地集成第三方组件（电子邮件，任务，调度，缓存等等）、对单元测试支持比较好、支持 RESTful Java 应用程序的开发。

###  Spring Framework 中有哪些模块？

下图对应的是 `Spring4.x` 版本。目前最新的 `5.x` 版本中 `Web` 模块的 `Portlet` 组件已经被废弃掉，同时增加了用于异步响应式处理的 WebFlux 组件。

![](https://resource.lzyan.fun/PigGo/20220302162856.png)

**主要模块作用：**

- **Spring Core**

核心模块， `Spring` 其他所有的功能基本都需要依赖于该类库，主要提供 `IoC` 依赖注入功能的支持。

- **Spring Aspects**

该模块为与 `AspectJ` 的集成提供支持。

- **Spring AOP**

提供了面向切面的编程实现。

- **Spring Data Access/Integration**

主要由 5 个模块组成：

1. `spring-jdbc` : 提供了对数据库访问的抽象 `JDBC`。不同的数据库都有自己独立的 `API` 用于操作数据库，而 `Java` 程序只需要和 `JDBC API` 交互，这样就屏蔽了数据库的影响。 
2. `spring-tx` : 提供对事务的支持。 
3. `spring-orm` : 提供对 `Hibernate` 等 `ORM` 框架的支持。 
4. `spring-oxm` ： 提供对 `Castor` 等 `OXM` 框架的支持。 
5. `spring-jms` : `Java` 消息服务。

- **Spring Web**

主要由 4 个模块组成：

1. `spring-web` ：对 Web 功能的实现提供一些最基础的支持。
2. `spring-webmvc` ： 提供对 `Spring MVC` 的实现。 
3. `spring-websocket` ： 提供了对 `WebSocket` 的支持，`WebSocket` 可以让客户端和服务端进行双向通信。 
4. `spring-webflux` ：提供对 `WebFlux` 的支持。`WebFlux` 是 `Spring Framework 5.0` 中引入的新的响应式框架。与 `Spring MVC` 不同，它不需要 `Servlet API`，是完全异步.

- **Spring Test**

`Spring` 团队提倡测试驱动开发（TDD）。有了控制反转 (IoC)的帮助，单元测试和集成测试变得更简单。 `Spring` 的测试模块对` JUnit（单元测试框架）`、`TestNG（类似 JUnit）`、`Mockito（主要用来 Mock 对象）`、`PowerMock`（解决 Mockito 的问题比如无法模拟 final, static， private 方法）等等常用的测试框架支持的都比较好。

### 不同版本的 Spring Framework 有哪些主要功能？

![](https://resource.lzyan.fun/PigGo/20220302172243.png)

从 Spring1.x 到 Spring5.x ，这里做下简单说为神马是 1.x ，其实 Spring 的版本分为三个部分

第一部分就是主版本，就是我们看到的 1～5

第二部分就是从版本，比如：1.0，1.1 包括 2.0，2.5 这样的版本，主要针对主版本多一些简单的更新

第三部分就是次要版本，比如：1.0.1 主要是一个 Bug Fix 或者是部分上面的提升

从上面可以看出 Java 版本分为 Java 标准版本和 Java 企业版本；从 Java5 做了分水岭，Java5 之前标准版本叫 J2SE 就是 Java 2 后面 Standrad E 这个版本，

另外一个就是 Java 企业版本，从 Java EE 之前叫 J2EE，包括 1.0 到 1.4 的版本。

- **Spring 1.x**

`Spring Framework 1.x` 版本，支持 Java 1.3 ，那么为什么会到 Java1.3。其实非常有道理，因为 Spring Framework 早起版本叫 interface21，当时这个版本依赖于 java1.3，1.3 引入一个重要概念-动态代理

从 Java1.3 开始就会针对接口的方式进行动态处理，这里实现 AOP 的一个重要环节，因此 Spring 的第一个版本就必须依赖于 Java1.3 ，AOP 也就是 Spring Framework 的一个重要部分，于此同时它支持的 Java EE 版本也是 1.3 ，这个版本的一个简单特性就是 Servlet 的 API 就是 Java 版本的是 1.3，Servlet 是 2.3 这个版本，2.3 这个版本支持 Servlet 事件，那么因此它可以和我们的 Spring 里面的事件进行一个呼应，当然 Servlet 事件和 Java 事件都是 Java 标准事件的实现

- **Spring 2.x**

其实这个版本在一些老的项目里面比较容易常见，比如 Spring Framework2.5.6 这个版本，还包括 Spring Framework 2.5.6 ESC 这样的版本，那就是 Security 的缩写，那就是针对安全部分做重新上面的设计和一些提升，它依赖 Java 的标准版本是 Java1.4.2，这个版本支持包括我们常说的 NIO 的支持此时，J2EE 版本并没有做太多的更新还是支持到 J2EE 1.3 版本。

- **Spring 3.x**

Spring 3.x 是一个重大版本，那么 Spring 从 3 这个版本开始引入了大量的注解，所以它所需要支持的 Java5 这个版本，在 Java5 里面会提升到一些注解，包括注解&枚举这些，所以在 Spring3 里面会引入大量的注解&枚举，所以这个时候对 Java 版本的最低要求就是 java5，对应 J2EE 1.4 版本和 J2EE1.5 版本

Spring 3.x 为什么是一个重要版本，因为 Spring3.x 基本确定了 Spring Framework 的内核，这个内核比较多，包括注解驱动，事件驱动包括一些 AOP 的支持，它在这个版本做的都比较完善

- **Spring 4.x**

Spring 4.x 这个版本主要是对 Spring3.x 版本增加了一些新的东西，主要是一些细节上的东西，包括注解上面的提升，对 Java 支持只需要 Java6 版本以上就行，这就是为了照顾到更多的人去使用 Spring Framework 4 这个版本，那么于此同时从 Spring Framework 4 开始也是对 Sping Boot 1.x 的支持

也就是说 Spring Boot 1.x 版本也是基础 Spring 4 进行开发的，Spring Boot 2 是基础 Spring 5 来开发的

- **Spring 5.x**

Spring Boot 2 是基础 Spring 5 来开发的，从 Spring 4 开始 Spring 就慢慢摆脱 J2EE 的局限性形成自己的体系包括后面延伸出来的 Spring Boot & Spring Cloud

### Spring 框架中用到了哪些设计模式？

- **工厂设计模式** : Spring 使用工厂模式通过 BeanFactory、ApplicationContext 创建 bean 对象。 

- **代理设计模式** : Spring AOP 功能的实现。

- **单例设计模式** : Spring 中的 Bean 默认都是单例的。 

- **模板方法模式** : Spring 中 jdbcTemplate、hibernateTemplate 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。 

- **包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。 

- **观察者模式**: Spring 事件驱动模型就是观察者模式很经典的一个应用。 

- **适配器模式** : Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配Controller。 

- ......

详细介绍：[面试官:“谈谈Spring中都用到了那些设计模式?”。——JavaGuide](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485303&idx=1&sn=9e4626a1e3f001f9b0d84a6fa0cff04a&chksm=cea248bcf9d5c1aaf48b67cc52bac74eb29d6037848d6cf213b0e5466f2d1fda970db700ba41&token=255050878&lang=zh_CN#rd)

## IoC

### 什么是 IoC？

`IoC （Inversion of control ）控制反转/反转控制`。它是一种思想不是一个技术实现。描述的是：`Java` 开发领域对象的创建以及管理的问题。

例如：现有类 `A` 依赖于类 `B`

- **传统的开发方式** ：往往是在类 `A` 中手动通过 `new` 关键字来 `new` 一个 `B` 的对象出来

- **使用 IoC 思想的开发方式** ：不通过 `new` 关键字来创建对象，而是通过 `IoC` 容器(Spring 框架) 来帮助我们实例化对象。我们需要哪个对象，直接从 `IoC` 容器里面过去即可。

从以上两种开发方式的对比来看：我们 “丧失了一个权力” (创建、管理对象的权力)，从而也得到了一个好处（不用再考虑对象的创建、管理等一系列的事情）

为什么叫 `控制反转` ：`IoC` 的思想就是两方之间不互相依赖，由第三方容器来管理相关资源。

- **控制** ：指的是对象创建（实例化、管理）的权力

- **反转** ：控制权交给外部环境（Spring 框架、IoC 容器）

![](https://resource.lzyan.fun/PigGo/20220302215226.png)

这样的好处就是，使对象之间的耦合度或者说依赖程度降低、资源变的容易管理，比如用 `Spring` 容器提供的话很容易就可以实现一个单例。

### IoC 和 DI 的区别？

`IoC（Inverse of Control:控制反转）`是一种 `设计思想` 或者说是某种模式。这个设计思想就是 将原本在程序中手动创建对象的控制权，交由 `Spring` 框架来管理。 `IoC` 在其他语言中也有应用，并非 `Spring` 特有。

`IoC` 最常见以及最合理的 `实现方式` 叫做 `依赖注入（Dependency Injection，简称 DI）`。在 `Spring` 中通过反射来把对象实例化后，而对象的具体属性还没有注入的时候，此时就是需要 `DI` 进行注入，比如：对象 `UserService` 依赖 `SendService` 对象，但是通过反射实例化的了 `UserService` ，这时候的 `SendService` 还是 `null` 的，然后下一步就是通过 `DI` 把对象的相关属性给注入了。

`IoC` 容器是 `Spring` 用来实现 `IoC` 的载体， `IoC` 容器实际上就是个 `Map（key，value`）,`Map` 中存放的是各种对象。`Spring` 时代我们一般通过 `XML` 文件来配置 `Bean`，后来开发人员觉得 `XML` 文件来配置不太好，于是 `SpringBoot` 注解配置就慢慢开始流行起来。

### 可以通过多少种方式完成 DI ？

通常，依赖注入可以通过三种方式完成，即：`构造函数注入`、`setter 注入`、`接口注入`。

在 `Spring` 中，仅使用 `构造函数注入` 和 `setter 注入`，其中 `setter 注入` 是最常使用的。

`setter 注入` 的实现例子：

1. 创建 `UserService` 接口，并在接口内定义一个 `print()` 方法。

```java
package com.lzyan.ioc
public interface UserService{
    void print();
}
```

2. 创建 `UserService` 接口的实现类 `UserServiceImpl`，并在类中声明一个 `userDao` 属性，以及对应的 `setter` 方法，`UserDao` 是一个普通类且定义了一个 `print()` 方法。

```java
package com.lzyan.ioc
public class UserServiceImpl{
    public UserDao userDao;

    public void SetUserDao(UserDao userDao){
        this.userDao = userDao;
    }

    @Override
    public void print(){
        this.userDao.print();
        System.out.println("UserServiceImpl print");
    }
}
```

3. 在配置文件 `applicationContext.xml` 中，创建一个 `id` 为 `userService` 的 `Bean` ，改 `Bean` 用于实例化 `UserServiceImpl` 类的信息，并将 `userDao` 的实例注入到 `userService` 中。`<property>` 是`<bean>` 元素的子元素，它用于调用 `Bean` 实例中的 `setUserDao()` 方法完成属性赋值，从而实现依赖注入。其 `name` 属性表示 `Bean` 实例中的相应属性名，`ref` 属性用于指定其属性值。

```xml
<bean id="userService" class="com.lzyan.ioc.UserServiceImpl>
    <property name = "userDao" ref="userDao" />
</bean>
```

4. 最后创建创建测试类 `Test` 进行测试

```java
package com.lzyan.ioc;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Text{
    public static void main(String[] args){
        // 初始化Spring容器，加载配置文件
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        // 通过容器获取UserService实例
        UserService userService = (UserService) applicationContext.getBean("userService");
        // 调用实例的print()方法
        userService.print();
    }
}
```

5. 输出

```java
userDao print
usereServce print
```

### Spring 中有多少种 IOC 容器？

在 `Spring` 中，有两种 `IOC` 容器：`BeanFactory` 和 `ApplicationContext`

- **BeanFactory**：`BeanFactory` 提供了 `Spring` 容器的配置框架和基本功能。

- **ApplicationContext**: `BeanFactory` 的子接口。它还扩展了其他一些接口，以支持更丰富的功能，如：国际化、访问资源、事件机制、更方便的支持 `AOP`、在 `web` 应用中指定应用层上下文等。

实际开发中，更推荐使用 `ApplicationContext` 作为 `IoC` 容器，因为它的功能远多于 `FactoryBean`。

### Spring IoC 的实现机制？

`Spring` 中的 `IoC` 的实现原理就是工厂模式加反射机制。

```java
interface Fruit {
     public abstract void eat();
}
class Apple implements Fruit {
    public void eat(){
        System.out.println("Apple");
    }
}
class Orange implements Fruit {
    public void eat(){
        System.out.println("Orange");
    }
}
class Factory {
    public static Fruit getInstance(String ClassName) {
        Fruit f=null;
        try {
            f=(Fruit)Class.forName(ClassName).newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return f;
    }
}
class Client {
    public static void main(String[] a) {
        Fruit f=Factory.getInstance("io.github.dunwu.spring.Apple");
        if(f!=null){
            f.eat();
        }
    }
}
```

## AOP

### 什么是 AOP？

`AOP (Aspect-Oriented Programming:面向切面编程)` 能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块的耦合度，并有利于未来的可扩展性和可维护性。通俗地理解其实就是在方法前后增加非业务代码。

与 `OOP( Object-Oriented Programming 面向对象编程)` 相辅相成，提供了与 `OOP` 不同的抽象软件结构的视角， 在 `OOP` 中以 `类(class)` 作为基本单元, 而 `AOP` 中的基本单元是 `Aspect(切面)`。

`AOP` 主要用来解决：在不改变原有业务逻辑的情况下，增强横切逻辑代码，根本上解耦合，避免横切逻辑代码重复。`OOP` 能解决大部分的代码重复问题，但是有一些处理不了的例如在类中的多个方法的相同位置出现的相同代码。

### Spring AOP and AspectJ AOP 有什么区别？

`Spring AOP` 属于运行时增强，而 `AspectJ` 是编译时增强。 `Spring AOP` 基于代理(Proxying)，而 `AspectJ` 基于字节码操作(Bytecode Manipulation)。又或者说 `Spring AOP` 基于动态代理方式实现，`AspectJ` 基于静态代理方式实现。

- 静态代理 - 指使用 `AOP` 框架提供的命令进行编译，从而在编译阶段就可生成 `AOP` 代理类，因此也称为编译时增强；
    - 编译时编织（特殊编译器实现）
    - 类加载时编织（特殊的类加载器实现）

- 动态代理 - 在运行时在内存中“临时”生成 `AOP` 动态代理类，因此也被称为运行时增强。
    - JDK 动态代理
    - CGLIB

通过 动态代理 技术来实现 `AOP` 代理, 可以为任意的接口实现代理，如果需要为一个类实现代理, 那么可以使用 `CGLIB` 代理，当一个业务逻辑对象没有实现接口时, 那么 `Spring AOP` 就默认使用 `CGLIB` 来作为 `AOP` 代理了，即如果需要为一个方法织入 `advice`, 但是这个方法不是一个接口所提供的方法, 则此时 `Spring AOP` 会使用 `CGLIB` 来实现动态代理。鉴于此, `Spring AOP` 建议基于接口编程, 对接口进行 `AOP` 而不是类。

`Spring AOP` 已经集成了 `AspectJ` ，`AspectJ` 应该算的上是 `Java` 生态系统中最完整的 `AOP` 框架了。`AspectJ` 相比于 `Spring AOP` 功能更加强大，但是 `Spring AOP` 相对来说更简单。如果切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 `AspectJ` ，它比 `Spring AOP` 快很多。

## Bean

### 什么是 Bean？

简单来说，`bean` 代指的就是那些被 `IoC` 容器所管理的对象，是构成用户 应用程序 主干的对象。

我们需要告诉 `IoC` 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 `XML` 文件、注解或者 `Java` 配置类。


### 普通 Java 对象和 Spring 所管理的 Bean 实例化的过程的区别？

在普通 `Java` 环境下创建对象简要的步骤可以分为：

1. java 源码被编译为被编译为 class 文件
2. 等到类需要被初始化时（比如说new、反射等）
3. class 文件被虚拟机通过类加载器加载到 JVM
4. 初始化对象供我们使用

简单来说可以理解为它是用 `Class对象` 作为 `模板` 进而创建出具体的实例。

而 `Spring` 所管理的 `Bean` 不同的是，除了 `Class对象` 之外，还会使用 `BeanDefinition` 的实例来描述对象的信息。比如说，可以在 `Spring` 所管理的 `Bean` 有一系列的描述：@Scope、@Lazy、@DependsOn 等等。也就是 `Spring` 有 `BeanDefinition` 来存储着我们日常给 `Spring Bean` 定义的元数据（@Scope、@Lazy、@DependsOn等等）

可以理解为：`Class` 只描述了类的信息，而 `BeanDefinition` 描述了对象的信息。

### bean 的作用域有哪些？

`Spring` 中的 `bean` 默认都是单例的，`Spring` 的单例是基于 `BeanFactory` 也就是 `Spring` 容器的，单例 `Bean` 在此容器内只有一个，`Java` 的单例是基于 `JVM`，每个 `JVM` 内只有一个实例。

`Spring` 中 `Bean` 的作用域通常有下面几种：

- **singleton** : 当一个 bean 的作用域为 singleton，那么Spring IoC容器中只会存在一个共享的 bean 实例，并且所有对 bean 的请求，只要 id 与该 bean 定义相匹配，则只会返回bean的同一实例

- **prototype** : 当一个 bean 的作用域为 prototype，表示一个 bean 定义对应多个对象实例。prototype 作用域的 bean 会导致在每次对该 bean 请求（将其注入到另一个 bean 中，或者以程序的方式调用容器的 getBean() 方法）时都会创建一个新的 bean 实例。prototype 是原型类型，它在我们创建容器的时候并没有实例化，而是当我们获取bean的时候才会去创建一个对象，而且我们每次获取到的对象都不是同一个对象。根据经验，对有状态的 bean 应该使用 prototype 作用域，而对无状态的 bean 则应该使用 singleton 作用域。

- **request** : request只适用于Web程序，每一次 HTTP 请求都会产生一个新的bean，同时该bean仅在当前HTTP request内有效，当请求结束后，该对象的生命周期即告结束。

- **session** : session只适用于Web程序，session 作用域表示该针对每一次 HTTP 请求都会产生一个新的 bean，同时该 bean 仅在当前 HTTP session 内有效.与request作用域一样，可以根据需要放心的更改所创建实例的内部状态，而别的 HTTP session 中根据 userPreferences 创建的实例，将不会看到这些特定于某个 HTTP session 的状态变化。当HTTP session最终被废弃的时候，在该HTTP session作用域内的bean也会被废弃掉。

- **global-session** ：全局 session 作用域，仅仅在基于 portlet 的 web 应用中才有意义，Spring5 已经没有了。

配置 `bean` 作用域的方法有：

- xml 方式：

```java
<bean id="..." class="..." scope="singleton"></bean>
```

- 注解方式

```java
@Bean
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Person personPrototype() {
    return new Person();
}
```

### 单例 bean 的线程安全问题

单例 `bean` 存在线程问题，主要是因为当多个线程操作同一个对象的时候是存在资源竞争的。

常见的有两种解决办法：

1. 在 `bean` 中尽量避免定义可变的成员变量。
2. 在类中定义一个 `ThreadLocal` 成员变量，将需要的可变成员变量保存在 `ThreadLocal` 中（推荐的一种方式）。

不过，大部分 `bean` 实际都是无状态（没有实例变量）的（比如 Dao、Service），这种情况下， `bean` 是线程安全的。

### bean 的生命周期？

Spring 使用 `BeanDefinition` 来装载着给 Bean 定义的元数据，实例化 Bean 的时候实际上就是遍历 `BeanDefinitionMap`，Spring 的 Bean 实例化和属性赋值时分开两步来做的，在 Spring Bean 的生命周期，Spring 预留了很多的 hook 可以给我们去扩展。实际过程简要如下：

1. Bean 容器找到配置文件中 Spring Bean 的定义。
2. Bean 容器利用 Java Reflection API 创建一个 Bean 的实例。
3. 如果涉及到一些属性值 利用 set()方法设置一些属性值（使用依赖注入填充所有属性）。
4. 如果 Bean 实现了 BeanNameAware 接口，调用 setBeanName()方法，传入 Bean 的 ID。
5. 如果 Bean 实现了 BeanFactoryAware 接口，调用 setBeanFactory()方法，传入 BeanFactory对象的实例。
6. 与上面的类似，如果实现了其他 *.Aware接口，就调用相应的方法。
7. 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessBeforeInitialization() 方法
8. 如果 Bean 实现了InitializingBean接口，执行afterPropertiesSet()方法。
9. 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
10. 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessAfterInitialization() 方法。
11. 经过以上的工作后，Bean 将一直驻留在应用上下文中给应用使用，直到应用上下文被销毁。
12. 如果 Bean 实现了 DispostbleBean 接口，Spring 将调用它的 destory 方法，作用与在配置文件中对 Bean 使用 destory-method 属性的作用一样，都是在 Bean 实例销毁前执行的方法。

![](https://resource.lzyan.fun/PigGo/20220304133159.png)

更详细的图解，出自 [如何叙述Spring Bean 的生命周期，让面试官眼前一亮！](https://www.bilibili.com/video/BV12a411r75X?spm_id_from=333.880.my_history.page.click)

![](https://resource.lzyan.fun/PigGo/springbean2.png)

![](https://resource.lzyan.fun/PigGo/springbean1.png)


### Spring 中配置 bean 的方式有哪些？

1. **基于 xml 配置**

在 `Spring1.x` 时代，都是基于 `xml` 来进行配置，用 `xml` 文件来管理 bean 之间的关系。bean 所需的依赖项和服务在 `XML` 格式的配置文件中指定。这些配置文件通常包含许多 bean 定义和特定于应用程序的配置选项。它们通常以 bean 标签开头。例如：

```java
<bean id="studentbean" class="org.edureka.firstSpring.StudentBean">
 <property name="name" value="Edureka"></property>
</bean>
```

2. **基于注解配置**

由于项目越来越大，过多的使用 `xml` 配置会变得不太方便（经常要在java文件和xml文件之间来回切换）,所以在 `Spring2.x` 时代，Spring 提供了声明 bean 的注解，大大减少了配置量。注解的配置原则是：基本配置使用 xml（如数据库的配置），业务配置使用注解。

在 Bean 的定义上： 使用 `@Component` 或其 子类（@Repository、@Service、@Controller）来定义 bean。

在 Bean 的注入上：

- `@required` 注解：应用于 bean 的 setter 方法，表示被标注过的属性必须在XML文件中配置，否则会出现异常。
- `@Autowire` 注解：在属性上使用 @Autowire 注解可以进行自动装配，减少代码。
- 当需要创建多个相同类型的 bean，并且只需要装配其中一个时，可以使用 @Qualifier 和 @Autowire 用来指定装载其中一个 bean。

3. **基于 Java API 配置**

Spring 的 Java 配置是通过使用 `@Bean` 和 `@Configuration` 来实现。`Spring3.0` 以后，提供了 Java 配置的能力，也就是 `JavaConfig` ，`Spring4.x` 和 SpringBoot 都推荐使用 Java 配置。

> JavaConfig 是Spring 社区的产品，他提供了配置Spring IOC容器的纯Java方法

- `@Bean` : 注解扮演与 `<bean />` 元素相同的角色。表示实例化一个 bean，等同于在 xml 里面添加一个 bean 被 @Bean 修饰的方法名就是该 bean 的 name。
- `@Configuration` ：表示修饰的类可以作为bean的来源（通过注解来获取bean）

例如：
```java
@Configuration
public class StudentConfig {
    @Bean
    public StudentBean myStudent() {
        return new StudentBean();
    }
}
```

转自：[Spring三种配置方式](https://blog.csdn.net/qq_35744081/article/details/94986758?spm=1001.2101.3001.6650.7&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-7.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-7.pc_relevant_default&utm_relevant_index=11)

### Spring 自动装配是什么？

自动装配就是让应用程序上下文为你找出依赖项的过程。简单点说，就是 `Spring` 会在上下文中自动查找，并自动给 `bean` 装配与其关联的属性。

`spring` 中实现自动装配的方式有两种，一种是通过 `xml` 文件、另一种是通过注解。

自动装配的不同模式：

- **no** - 这是默认设置，表示没有自动装配。应使用显式 bean 引用进行装配。

- **byName** - 它根据 bean 的名称注入对象依赖项。它匹配并装配其属性与 XML 文件中由相同名称定义的 bean。

- **byType** - 它根据类型注入对象依赖项。如果属性的类型与 XML 文件中的一个 bean 名称匹配，则匹配并装配属性。

- **构造函数** - 它通过调用类的构造函数来注入依赖项。它有大量的参数。

- **autodetect** - 首先容器尝试通过构造函数使用 autowire 装配，如果不能，则尝试通过 byType 自动装配

详细参考：[Spring自动配置](https://www.cnblogs.com/bear7/p/12531016.html)

## 注解

### @Component 和 @Bean 的区别是什么？

1. `@Component` 注解作用于类，而 `@Bean` 注解作用于方法。
2. `@Component` 通常是通过类路径扫描来自动侦测以及自动装配到 `Spring` 容器中（我们可以使用 `@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 `Spring` 的 `bean` 容器中）。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 `bean`, `@Bean` 告诉了 `Spring` 这是某个类的实例，当我需要用它的时候还给我。
3. `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 `bean`。比如当我们引用第三方库中的类需要装配到 `Spring` 容器时，则只能通过 @Bean来实现。

`@Bean` 注解使用示例：

```java
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }

}
```

相当于 `xml` 配置：

```xml
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

下面这个例子是通过 `@Component` 无法实现的。

```java
@Bean
public OneService getService(status) {
    case (status)  {
        when 1:
                return new serviceImpl1();
        when 2:
                return new serviceImpl2();
        when 3:
                return new serviceImpl3();
    }
}
```

### 将一个类声明为 bean 的注解有哪些？

我们一般使用 `@Autowired` 注解自动装配 `bean`，要想把类标识成可用于 `@Autowired` 注解自动装配的 `bean` 的类,采用以下注解可实现：

- **@Component** ：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用@Component 注解标注。 

- **@Repository** : 对应持久层即 Dao 层，主要用于数据库相关操作。

- **@Service** : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。 

- **@Controller** : 对应 Spring MVC 控制层，主要用户接受用户请

### Spring 中 @Autowired 和 @Resource 的区别？（阿里内推二面）

`@Resource` 和 `@Autowired` 都是做 `bean` 的注入时使用。@Autowired 功能虽说非常强大，但是也有些不足之处。比如：比如它跟spring强耦合了，如果换成了JFinal等其他框架，功能就会失效。而 `@Resource` 其实 并不是 `Spring` 的注解，它的包是 `javax.annotation.Resource`，需要导入，但是 `Spring` 支持该注解的注入。有些场景使用 `@Autowired` 无法满足的要求，改成 `@Resource` 却能解决问题。

- @Autowired 默认按 byType 自动装配，而 @Resource 默认 byName 自动装配。

- @Autowired 只包含一个参数：required，表示是否开启自动准入，默认是 true。而 @Resource 包含七个参数，其中最重要的两个参数是：name 和 type。

- @Autowired 如果要使用 byName，需要使用 @Qualifier 一起配合。而 @Resource 如果指定了 name，则用 byName 自动装配，如果指定了 type，则用 byType 自动装配。

- @Autowired 能够用在：构造器、方法、参数、成员变量和注解上，而 @Resource 能用在：类、成员变量和方法上。两者如果都写在变量上，那么就不需要再写setter方法。

- @Autowired 是 spring 定义的注解，而 @Resource 是 Java 标准定义的注解。

此外，它们的装配顺序不同。

`@Autowired` 的装配顺序如下：

![](https://resource.lzyan.fun/PigGo/20220304203712.png)

`@Resource` 的装配顺序：分四种情况，具体请看参考连接

参考：[@Autowired和@Resource的区别是什么？](https://www.zhihu.com/question/39356740)

### Spring 是怎么解决循环依赖的？

如果现在有个 A 对象，它的属性是 B 对象，而 B 对象的属性也是 A 对象，说白了就是A依赖B，而B又依赖A。他们之间互相持有对方，这就是循环依赖，例如：

```java
public class A{
    private B b;
}
public class B{
    private A a;
}
```

因为对象属性的注入是在对象实例化后，大致过程是：首先 A 对象实例化，然后对属性进行注入，发现依赖 B 对象，B 对象此时还没有创建出来，所以转头去实例化 B 对象，B 对象实例化后，发现需要依赖 A 对象，那 A 对象已经实例化了，所以 B 对象最终能完成创建，B 对象返回到 A 对象的属性注入的方法上，A 对象最终完成。

`spring` 的 `bean` 默认是单例的，如果单例 `bean` 使用 `@Autowired` 自动装配，大多数情况，能解决循环依赖问题。但是如果 `bean` 是多例的，会出现循环依赖问题，导致 `bean` 自动装配不了。还有有些情况下，如果创建了代理对象，即使 `bean` 是单例的，依然会出现循环依赖问题。

Spring 解决循环依赖的问题使用了三级缓存。所谓三级缓存定义是这样的：

- singletonObjects （一级，日常实际获取 Bean 的地方）
- earlySingletonObjects （二级，还没进行属性注入，由三级缓存放进来）
- singletonFactories （三级，Value是一个对象工厂）

再回到上述过程中的，A 对象实例化之后，属性注入之前，其实会把 A 对象放入三级缓存，key 是 BeanName ， Value 是 ObjectFactory 等到 A 对象属性注入时，发现依赖 B，然后又去实例化 B 时，B 的属性注入又需要获取 A 对象，这里就是从三级缓存中拿出 ObjectFactory，从 ObjectFactory 得到对应的 Bean （就是对象 A），把三级缓存中的 A 记录干掉，然后放到二级缓存中。显然，二级缓存存储的 key 时 BeanName，value 就是Bean （这里的 Bean 还没有做完属性注入相关的工作），等到完全初始化完之后，就会把二级缓存给 remove 掉，塞到一级缓存中。所以最后去 getBean 的时候，实际上拿到的时一级缓存的。这就是大概的过程

参考：[Spring 是如何解决循环依赖的？](https://www.zhihu.com/question/438247718/answer/2231407456)

### Spring/SpringBoot常用注解

巨详细说明：[Spring/Spring Boot 常用注解总结](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_1-springbootapplication)

## MVC

### 对 Spring MVC 的理解？

`MVC` 是 `模型（Model）`、 `视图（View）` 、 `控制器（Controller）` 的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。有些人说 MVC 不是设计模式，MVC 框架模式是一种复合模式，一种软件设计规范。但是也有认为 MVC 同样是众多设计模式中的一种，众说纷纭。



理解 `Spring MVC `要从 `Model1` 和 `Model2` 这两个没有 `Spring MVC` 的时代开始说起。

- **Model 1**

Model 1 时代中，整个 `Web` 应用几乎全部用 `JSP` 页面组成，只用少量的 `JavaBean` 来处理数据库连接、访问等操作。这个模式下 JSP 即是控制层（Controller）又是表现层（View）。显而易见，这种模式存在很多问题。比如控制逻辑和表现逻辑混杂在一起，导致代码重用率极低；再比如前端和后端相互依赖，难以进行测试维护并且开发效率极低。

- **Model 2**

Model 2 时代中，是 `Java Bean(Model)+ JSP（View）+Servlet（Controller）` 这种开发模式，这也是早期的 JavaWeb MVC 开发模式。

Model: 系统涉及的数据，也就是 dao 和 bean。

View：展示模型中的数据，只是用来展示。

Controller：处理用户请求都发送给 ，返回数据给 JSP 并展示给用户。

`Model2` 模式下还存在很多问题，抽象和封装程度还远远不够，使用 `Model2` 进行开发时不可避免地会重复造轮子，这就大大降低了程序的可维护性和复用性。

于是，很多 `JavaWeb` 开发相关的 `MVC` 框架应运而生比如 `Struts2`，但是 `Struts2` 比较笨重。

- **Spring MVC 时代**

随着 `Spring` 轻量级开发框架的流行，`Spring` 生态圈出现了 `Spring MVC` 框架， `Spring MVC` 是当前最优秀的 `MVC` 框架。相比于 `Struts2` ， `Spring MVC` 使用更加简单和方便，开发效率更高，并且 `Spring MVC` 运行速度更快。

常见的服务器端 MVC 框架有：Struts、Spring MVC、ASP.NET MVC、Zend Framework、JSF；
常见前端MVC框架：vue、angularjs、react、backbone；由MVC演化出了另外一些模式如：MVP、MVVM 等等....

### SpringMVC 工作流程？

Spring MVC 的工作流程可以用一幅图来说明：

![](https://resource.lzyan.fun/PigGo/20220305143902.png)

1. 向服务器发送 HTTP 请求，请求被前端控制器 DispatcherServlet 捕获。

2. DispatcherServlet 根据 -servlet.xml 中的配置对请求的 URL 解析，得到请求资源标识符（URI）。然后根据 该 URI 调用 HandlerMapping 获得该 Handler 配置的所有相关的对象（包括 Handler 对象以及 Handler 对象对应的拦截器），最后以 HandlerExecutionChain 对象的形式返回。

3. DispatcherServlet 根据获得的 Handler ，选择一个合适的 HandlerAdapter。（附注：如果成功获得 HandlerAdapter 后，此时将开始执行拦截器的 preHandler(...) 方法）。

4. 提取 Request 中的模型数据，填充 Handler 入参，开始执行 Handler(Controller 。 在填充 Handler 的入参过程中，根据你的配置，Spring 将帮你做一些额外的工作：
    - HttpMessageConveter： 将请求消息（如 Json、xml 等数据）转换成一个对象，将对象转换为指定的响应信息。
    - 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等。
    - 数据根式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等。
    - 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中。

5. Handler(Controller)执行完成后，向 DispatcherServlet 返回一个 ModelAndView 对象；

6. 根据返回的ModelAndView，选择一个适合的 ViewResolver（必须是已经注册到 Spring 容器中的ViewResolver)返回给DispatcherServlet。

7. ViewResolver 结合Model和View，来渲染视图。

8. 视图负责将渲染结果返回给客户端。

## 事务

### 关于事务

在软件开发领域，全有或全无的操作被称为 `事务（transaction）`。事务允许你将几个操作组合成一个 **要么全部发生要么全部不发生** 的工作单元。

事务有四大特性： ACID

- **原子性（Atomic）**：一个事务是一个不可分割的工作单位，事务中包括的诸操作要么都做，要么都不做。

- **一致性（Consistent）**：事务必须是使数据库从一个一致性状态变到另一个一致性状态。一致性与原子性是密切相关的。

- **隔离性（Isolated）**：一个事务的执行不能被其他事务干扰。即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。

- **持久性（Durable）**：持久性也称永久性（permanence），指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响。

### Spring 管理事务的方式有什么？

- **编程式事务** ： 在代码中硬编码(不推荐使用) : 通过 TransactionTemplate 或者 TransactionManager 手动管理事务，实际应用中很少使用，但是对于你理解 Spring 事务管理原理有帮助。

- **声明式事务** ： 在 XML 配置文件中配置或者直接基于注解（推荐使用） : 实际是通过 AOP 实现（基于@Transactional 的全注解方式使用最多）

[Spring事务总结](https://javaguide.cn/system-design/framework/spring/spring-transaction/#_3-2-spring-%E4%BA%8B%E5%8A%A1%E7%AE%A1%E7%90%86%E6%8E%A5%E5%8F%A3%E4%BB%8B%E7%BB%8D)

### Spring 事务失效的场景？

spring开启事务的方式：

```java
@Service
public class ServiceImpl implements Service {
    @Transactional(rollbackFor = Exception.class)
    public void update(string xxxx) {
        // update  logic
    }
}
```

以下几种情况会导致开启的事务失效

#### 1. 数据库引擎不支持事务

在 MySQL 数据库中存储引擎除了 innodb，其他都不支持事务。从 MySQL 5.5.5 开始的默认存储引擎是：InnoDB，之前默认的都是：MyISAM。

#### 2.类没有被spring管理

比如上面的代码示例中的ServiceImpl类没有增加 @Service注 解，这个类就不会被加载成一个bean，不会被spring管理，事务也就失效了

#### 3.方法非public修饰

@Transactional只能用在public方法上面，原因是spring要求被代理的方法必须是public，否则事务不会生效。

#### 4.方法中调用同类方法

```java
@Service
public class ServiceImpl implements Service {
    public void test(string xxxx) {
        update(xxxx);
    }
    
    @Transactional(propagation =  Propagation.REQUIRED)
    public void update(string xxxx) {
            // update logic
    }
}
```

因为是 AOP，直接调用内部方法并非是代理类调用，所以会失效。在 springboot 中可以这样解决

```java
@Service
public class ServiceImpl implements Service {
    public void test(string xxxx) {
        Service proxy = (Service) AopContext.currentProxy();
        proxy.update(xxxx);
    }
    
    @Transactional(propagation =  Propagation.REQUIRED)
    public void update(string xxxx) {
            // update logic
    }
}
```

#### 5.抛出的异常被捕获

```java
@Service
public class ServiceImpl implements Service {
    @Transactional
    public void update(string xxxx) {
        try {
            // update logic
        } catch {
        }
    }
}
```
当业务方法抛出异常，spring 感知到异常的时候，才会做事务回滚的操作，若异常被方法内部 try catch 捕获，外部事务不会捕获异常触发回滚

#### 6.事务的传播机制配置错误

```java
@Service
public class ServiceImpl implements Service {
    @Transactional(propagation =  Propagation.NEVER)
    public void update(string xxxx) {
        try {
            // update logic
        } catch {
        }
    }
}
```
#### 7.rollbackFor属性设置错误

```java
@Service
public class ServiceImpl implements Service {
    @Transactional(rollbackFor = NullPointerException.class)
    public void update(string xxxx) {
            // update logic
    }
}
```
rollbackFor 用于指定能够触发事务回滚的异常类型，可以指定多个异常类型。默认是在RuntimeException和Error上回滚。若异常非配置指定的异常类，则事务失效

#### 8.noRollbackFor属性设置错误

和rollbackFor互补使用，用法相反

#### 9.被final修饰的方法

```java
@Service
public class ServiceImpl implements Service {
    @Transactional(rollbackFor = Exception.class)
    public final void update(string xxxx) {
        // update logic
    }
}
```
final 修饰的方法表明方法不能被子类重写 spring 事务的底层是通过 aop 来创建一个代理类，而代理类无法重写该方法，无法实现事务功能。

参考:[Spring中事务失效的几种情况](https://zhuanlan.zhihu.com/p/514257018)


## Boot

### SpringBoot 自动装配实现原理简要

自动装配的思想，一般会和 SpringBoot 联系在一起。但是，实际上 Spring Framework3.x 的 `@Enable` 注解已经有这个雏形。SpringBoot 只是在其基础上，通过 `SPI` 的方式，做了进一步优化。

> SPI ，全称为 Service Provider Interface，是JDK内置一种服务发现机制。它通过在 classpath 路径下的 META-INF/services 文件夹查找文件，自动加载文件里所定义的类。这一机制为很多框架的扩展提供了可能，比如Dubbo、JDBC中都是用了SPI机制。

什么是自动装配，简单来说，当在 SpringBoot 中需要引入一个第三方的组件，此时只要直接引入一个 `starter` 即可，然后通过一些简单的配置，自动装配就可以自动去把第三方组件的 Bean ，装载到 `IOC` 容器里面，不需要开发人员去写 Bean 的相关配置。

在 SpringBoot 应用中只需要在启动类上面加上` @SpringBootApplication` 注解就可以实现自动装配了。而这个 `@SpringBootApplication` 注解是一个复合的注解，其中真正去实现自动装配的是 `@EnableAutoConfiguration` 这个注解。

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    // 略
}
```

除了元注解外，后面三个注解的作用为：

- **@EnableAutoConfiguration**：启用 SpringBoot 的自动配置机制

- **@SpringBootApplication**：点开源码看实际上它就是一个 @Configuration 注解，允许在上下文中注册额外的 bean 或导入其他配置类。这样子是为了语义化一点？

- **@ComponentScan**：扫描被 @Component (@Service,@Controller) 注解的 bean，注解默认会扫描启动类所在的包下所有的类 ，可以自定义不扫描某些 bean。 如代码所示容器中将排除 TypeExcludeFilter 和 AutoConfigurationExcludeFilter 这两个

    - TypeExcludeFilter：SpringBoot 对外提供的扩展类， 可以供我们去按照我们的方式进行排除
    - AutoConfigurationExcludeFilter：排除所有配置类并且是自动配置类中里面的其中一个

从 `@EnableAutoConfiguration` 这个注解去入手，发现它也只是一个简单的注解

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage // 将当前配置类所在包保存在BasePackages的Bean中。供Spring内部使用
@Import({AutoConfigurationImportSelector.class})  //
public @interface EnableAutoConfiguration {
  // 略
}
```

所以其自动装配核心功能的实现实际是通过 `@Import` 这个注解中的 `AutoConfigurationImportSelector.class` 。`AutoConfigurationImportSelector` 实现了 `DeferredImportSelector` 这个接口也就是实现了 `ImportSelector` 接口（DeferredImportSelector extends ImportSelector），并且实现了接口中的 `selectImports` 方法。该方法主要用于获取所有符合条件的类的全限定类名，这些类需要被加载到 IoC 容器中。

![](https://resource.lzyan.fun/PigGo/20220326165751.png)

`Spring` 内部在解析 `@Import` 注解时会调用 `getAutoConfigurationEntry` 方法，这块属于Spring的源码，先不管它是怎么调用的。`getAutoConfigurationEntry` 这个方法主要负责加载自动配置类。下面是 2.3.5.RELEASE 实现源码：

```java
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    // 判断自动装配开关是否打开。默认 spring.boot.enableautoconfiguration=true，可在 application.properties 或 application.yml 中设置
   if (!isEnabled(annotationMetadata)) {
      return EMPTY_ENTRY;
   }
   // 用于获取EnableAutoConfiguration注解中的 exclude 和 excludeName
   AnnotationAttributes attributes = getAttributes(annotationMetadata);
   // 从 META-INF/spring.factories 中获得候选的自动配置类
   List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
   // 排重
   configurations = removeDuplicates(configurations);
   //根据 EnableAutoConfiguration 注解中属性，获取不需要自动装配的类名单
   Set<String> exclusions = getExclusions(annotationMetadata, attributes); 
   // 根据:@EnableAutoConfiguration.exclude
   // @EnableAutoConfiguration.excludeName
   // spring.autoconfigure.exclude  进行排除
   checkExcludedClasses(configurations, exclusions);
   // exclusions 也排除
   configurations.removeAll(exclusions);
   // 通过读取 spring.factories 中的 OnBeanCondition\OnClassCondition\OnWebApplicationCondition 进行过滤
   // 因为 spring.factories 中这么多配置每次启动都不需要全部加载的
   configurations = getConfigurationClassFilter().filter(configurations);
   // 这个方法是调用实现了AutoConfigurationImportListener  的bean..  分别把候选的配置名单，和排除的配置名单传进去做扩展
   fireAutoConfigurationImportEvents(configurations, exclusions);
   return new AutoConfigurationEntry(configurations, exclusions);
}
```
任何一个 SpringBoot 应用，都会引入 spring-boot-autoconfigure，上面说到的 `META-INF/spring.factories` 文件就是在该包下。不光是这个依赖下的 `META-INF/spring.factories` 被读取到，所有 Spring Boot Starter 下的 `META-INF/spring.factories` 都会被读取到。

`spring.factories` 文件是 Key=Value 形式，多个 Value 时使用 `/` 隔开，该文件中定义了关于初始化，监听器等信息，而真正使自动配置生效的 key 是 `org.springframework.boot.autoconfigure.EnableAutoConfiguration`

**总结：@EnableAutoConfiguration 注解通过 @SpringBootApplication 被间接的标记在了 SpringBoot 的启动类上。在 SpringApplication.run(...) 的内部就会执行 selectImports() 方法，找到所有 JavaConfig 自动配置类的全限定名对应的 class，然后将所有自动配置类加载到 Spring 容器中**

参考: 

[Spring Boot 自动装配原理——JavaGuide](https://javaguide.cn/system-design/framework/spring/spring-boot-auto-assembly-principles.html)

[Spring Boot 自动装配原理——图灵学院](https://note.youdao.com/noteshare?id=8805e97f26dc9661bc2ecdbf5ca22393)

[自动装配流程图](https://www.processon.com/view/link/5fc0abf67d9c082f447ce49b)


### SpringBoot 与 SPI

`SPI` ，全称为 `Service Provider Interface`，是 `JDK` 内置一种服务发现机制，实际上是 `基于接口的编程＋策略模式＋配置文件` 组合实现的动态加载机制。

它通过在 `classpath` 路径下的 `META-INF/services` 文件夹查找文件，自动加载文件里所定义的类。这一机制为很多框架的扩展提供了可能，比如 Dubbo、JDBC 中都是用了 SPI 机制。比如 java.sql.Driver 接口，其他不同厂商可以针对同一接口做出不同的实现， MySQL 和 PostgreSQL 都有不同的实现提供给用户，而 Java 的 SPI 机制可以为某个接口寻找服务实现。Java 中 SPI 机制主要思想是将装配的控制权移到程序之外，在模块化设计中这个机制尤其重要，其核心思想就是 `解耦`。如下图

![](https://resource.lzyan.fun/PigGo/20220327183026.png)

#### JDK 中的 SPI

一个接口可以有多个实现，例如定义一个支付接口，这时可以有微信支付、支付宝支付、抖音支付等方式。系统的设计者为了降低耦合，并不会硬编码在里面写死支付的方式，而是会由服务提供者来选择使用哪种方式。

定义一个 Pay 接口

```java
package main.com.lzyan;

public interface Pay {

    void pay(Integer money);

}
```

实现 Pay 接口使用微信支付的方式

```java
package main.com.lzyan;

public class WechatPay implements Pay {
    @Override
    public void pay(Integer money) {
        System.out.println("微信支付:" + money + "元");
    }
}
```

实现 Pay 接口使用支付宝支付的方式

```java
package main.com.lzyan;

public class ALiPay implements Pay {
    @Override
    public void pay(Integer money) {
        System.out.println("支付宝支付:" + money + "元");
    }
}

```

在 `classpath` 下 `src\main\resources` 创建一个目录 `META-INF\services`，并且在该目录下创建一个配置文件 `main.com.lzyan.Pay` （该文件名为 SPI 接口全路径），文件中的内容为 SPI 接口具体的实现类（可以为多个）

```java
main.com.lzyan.ALiPay
main.com.lzyan.WechatPay
```

测试，使用 JDK 的 `ServiceLoader` 来进行读取

```java
package main.com.lzyan;

import java.util.Iterator;
import java.util.ServiceLoader;

public class Main {
    public static void main(String[] args) {
        ServiceLoader<Pay> load = ServiceLoader.load(Pay.class);
        Iterator<Pay> iterator = load.iterator();
        while (iterator.hasNext()) {
            Pay next = iterator.next();
            next.pay(100);
        }
    }
}
```

最熟输出的结果

```java
支付宝支付:100元
微信支付:100元
```

通过该过程可以了解到 SPI 是面向接口编程，不关注接口的具体实现，只需要按照约定在 META-INF/services 目录下面, 以接口的全限定名称为名创建一个文件夹, 文件夹下再放具体的实现类的全限定名称, 系统就能根据这些文件,加载不同的实现类。而这一实现可以看到时通过 JDK 的 ServiceLoader 实现的，所以可以具体了解一下这个东西

首先看 ServiceLoader 类的成员变量

```java
public final class ServiceLoader<S> implements Iterable<S>{

    private static final String PREFIX = "META-INF/services/";

    // 代表被加载的类或者接口
    private final Class<S> service;

    // 用于定位，加载和实例化providers的类加载器
    private final ClassLoader loader;

    // 创建ServiceLoader时采用的访问控制上下文
    private final AccessControlContext acc;

    // 缓存providers，按实例化的顺序排列
    private LinkedHashMap<String,S> providers = new LinkedHashMap<>();

    // 懒查找迭代器
    private LazyIterator lookupIterator;
  
    ......
}
```

应用程序调用 ServiceLoader.load 方法，方法内先创建一个新的 ServiceLoader，并实例化该类中的成员变量，包括：
1. loader(ClassLoader类型，类加载器) 
2. acc(AccessControlContext类型，访问控制器)
3. providers(LinkedHashMap<String,S>类型，用于缓存加载成功的类)
4. lookupIterator(实现迭代器功能)

```java

/*
 *入口, 获取当前类的类加载器,然后调用下一个静态方法
 */
 public static <S> ServiceLoader<S> load(Class<S> service) {
     ClassLoader cl = Thread.currentThread().getContextClassLoader();
     return ServiceLoader.load(service, cl);
 }
 /*
 * 直接调用构造方法
 */
 public static <S> ServiceLoader<S> load(Class<S> service, ClassLoader loader)
 {
     return new ServiceLoader<>(service, loader);
 }
 /**
 * 调用reload
 */
 private ServiceLoader(Class<S> svc, ClassLoader cl) {
     service = Objects.requireNonNull(svc, "Service interface cannot be null");
     loader = (cl == null) ? ClassLoader.getSystemClassLoader() : cl;
     acc = (System.getSecurityManager() != null) ? AccessController.getContext() : null;
     reload();
 }
 /**
 * 直接实例化一个懒加载的迭代器
 */
 public void reload() {
     providers.clear();
     lookupIterator = new LazyIterator(service, loader);
 }
```

应用程序通过迭代器获取对象的实例，ServiceLoader 先判断成员变量 providers 对象中 (LinkedHashMap< String,S >类型) 是否有缓存实例对象，如果有缓存，直接返回。如果没有缓存，执行类的装载，实现如下：

1. 读取 META-INF/services/ 下的配置文件，获得所有能被实例化的类的名称，值得注意的是，ServiceLoader可以跨越 jar 包获取 META-INF 下的配置文件，具体加载配置的实现代码如下
2. 通过反射方法 Class.forName() 加载类对象，并用 instance() 方法将类实例化。
3. 把实例化后的类缓存到 providers 对象中，(LinkedHashMap< String,S >类型）然后返回实例对象。

```java
private class LazyIterator
        implements Iterator<S>
{

    Class<S> service;
    ClassLoader loader;
    Enumeration<URL> configs = null;
    Iterator<String> pending = null;
    String nextName = null;

    // 略

    private boolean hasNextService() {
        if (nextName != null) {
            // nextName不为空,说明加载过了,而且服务不为空 
            return true;
        }
        // configs就是所有的实现类文件名字
        if (configs == null) {
            try {
                // PREFIX是 /META-INF/services
                // service.getName() 是接口的全限定名称
                String fullName = PREFIX + service.getName();
                // loader == null, 说明是bootstrap类加载器(点进去源码查看可知)
                if (loader == null)
                    configs = ClassLoader.getSystemResources(fullName);
                else
                    // 加载该目录下的所有文件资源
                    configs = loader.getResources(fullName);
                } catch (IOException x) {
                    fail(service, "Error locating configuration files", x);
                }
        }
        while ((pending == null) || !pending.hasNext()) {
                if (!configs.hasMoreElements()) {
                    // 该目录下什么文件都没有
                    return false;
                }
                //就是判断一下configs.nextElement()的格式是不是对的
                pending = parse(service, configs.nextElement());
        }
        nextName = pending.next();
        return true;
    }

    private S nextService() {
     // 校验一下
     if (!hasNextService())
             throw new NoSuchElementException();
     String cn = nextName;
     nextName = null;
     Class<?> c = null;
     try {
         // 尝试一下是否能加载该类
         c = Class.forName(cn, false, loader);
     } catch (ClassNotFoundException x) {
         fail(service,"Provider " + cn + " not found");
     }
     // 是不是service的子类,或者同一个类
     if (!service.isAssignableFrom(c)) {
         fail(service,"Provider " + cn  + " not a subtype");
     }
     try {
         // 实例化这个类, 然后向上转一下
         S p = service.cast(c.newInstance());
         // providers 缓存起来,避免重复加载
         providers.put(cn, p);
         return p;
     } catch (Throwable x) {
         fail(service,"Provider " + cn + " could not be instantiated",x);
     }
     throw new Error();          // This cannot happen
    }

    // 略 

}
```

优点说了这么多，但其实 SPI 也有很多的不足：

1. 虽然 ServiceLoader 也算是使用的延迟加载，但是基本只能通过遍历全部获取，也就是接口的实现类全部加载并实例化一遍，不能按需加载，需要遍历配置文件中所有的实现并实例化。
2. 获取某个实现类的方式不够灵活，只能通过 Iterator 形式获取，不能根据某个参数来获取对应的实现类。
3. 多个并发多线程使用 ServiceLoader 类的实例是非线程安全的。

#### SpringBoot 中的 SPI

SpringBoot 中的 SPI 就是体现在 SpringBoot 的自动装配中。当在 SpringBoot 项目中需要使用一个组件的时候，仅需要引入一个组件的依赖，加个配置，这个组件就生效了。比如说要使用 Redis ，首先是在 pom.xml 添加以下依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

在 application.yml 配置文件进行配置 
```yaml
spring:
  redis:
    database: 0
    timeout: 5000ms
    host: 127.0.0.1
    port: 6379
    password: 123456
```

最后在使用的时候注入 RedisTemplate 就可以了

```java
@Autowired
private RedisTemplate redistemplate;
```

这一个过程就是通过 自动装配 来实现的，具体原理看上面说到的，通过原理理解下面自定义一个 SpringBoot 的 starter 来理解 SpringBoot 与 SPI 就很简单了

> 命名规范：
> 
> SpringBoot官方命名方式：spring-boot-starter-模块名
> 
> 自定义命名方式: 模块名-spring-boot-starter

创建一个普通的 SpringBoot 项目，在 pom.xml 文件引入

```java
<!--  引入 Spring Boot Starter 基础库   -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

编写一个实现类

```java
package com.lzyan;

public class PayClient {
    public void wechatPay(Integer money) {
        System.out.println("微信支付:" + money + "元");
    }

    public void aLiPay(Integer money) {
        System.out.println("支付宝支付:" + money + "元");
    }
}
```

编写 configuration 

```java
package com.lzyan;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AutoBeanConfiguration {

    @Bean
    public PayClient payClient() {
        return new PayClient();
    }
}
```

在 resource 下创建 META-INF/spring.factories 文件，添加以下内容内容。至此自定义的 starter 已经封装好，此时最好 install 一下，不然再使用的时候可能会找不到这个 starter 的 jar 包

```xml
# key 为 org.springframework.boot.autoconfigure.EnableAutoConfiguration
# val 对应的是自己编写的 Configuration 配置类
# val 可以是多个，多个最后要加 \ 符号
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.lzyan.AutoBeanConfiguration
```

创建一个新的 SpringBoot 项目，在 pom.xml 文件引入自定义的 starter 

```xml
<dependency>
    <groupId>com.lzyan</groupId>
    <artifactId>pay-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

然后编写一个类测试

```java
package com.lzyan;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class PayController {

    @Autowired
    private PayClient payClient;

    @GetMapping("/pay")
    public void pay() {
        payClient.wechatPay(100);
        payClient.aLiPay(200);
    }
}

```

输出的内容，以及目录分布

![](https://resource.lzyan.fun/PigGo/20220328175120.png)


## 参考

- [Spring各个版本引入了哪些新特性？](https://blog.csdn.net/li1669852599/article/details/107892432?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0.queryctrv4&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

- [Spring常见面试题](https://dunwu.github.io/spring-tutorial/summary/Spring%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98.html#_1-%E4%B8%80%E8%88%AC%E9%97%AE%E9%A2%98)

- [面试被问了几百遍的 IoC 和 AOP ，还在傻傻搞不清楚？](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247486938&idx=1&sn=c99ef0233f39a5ffc1b98c81e02dfcd4&chksm=cea24211f9d5cb07fa901183ba4d96187820713a72387788408040822ffb2ed575d28e953ce7&token=1736772241&lang=zh_CN#rd)

- [Spring常见问题总结](https://javaguide.cn/system-design/framework/spring/spring-knowledge-and-questions-summary/#)

- [Spring生命周期](http://javainterview.gitee.io/luffy/2021/08/19/05-Spring/03.%20SpringBean%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F/)

- [高级开发必须理解的Java中SPI机制](https://www.jianshu.com/p/46b42f7f593c)