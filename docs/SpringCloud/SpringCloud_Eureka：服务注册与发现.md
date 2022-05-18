

## Spring Cloud Eureka：服务注册与发现
> Spring Cloud Eureka是Spring Cloud Netflix 子项目的核心组件之一，主要用于微服务架构中的服务治理。


## 关于Eureka
在微服务架构中往往会有一个注册中心，每个微服务都会向注册中心去注册自己的地址及端口信息，注册中心维护着服务名称与服务实例的对应关系。每个微服务都会定时从注册中心获取服务列表，同时汇报自己的运行情况，这样当有的服务需要调用其他服务时，就可以从自己获取到的服务列表中获取实例地址进行调用，Eureka 实现了这套服务注册与发现机制。

本文将对搭建 Eureka 注册中心，搭建 Eureka 客户端，搭建 Eureka 集群及给 Eureka 注册中心添加登录认证进行介绍

## 搭建Eureka注册中心

创建一个 Empty 项目

![](https://resource.lzyan.fun/PigGo/2021-11-05_112322.png)


选择存放路径和命名

![](https://resource.lzyan.fun/PigGo/2021-11-05_112418.png)


新建module

![](https://resource.lzyan.fun/PigGo/20211105113338.png)

选择quickstart

![](https://resource.lzyan.fun/PigGo/20211105140649.png)

命名

![](https://resource.lzyan.fun/PigGo/20211105140121.png)

提供注册服务的服务器pom.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.lzyan</groupId>
    <artifactId>springcloud-eureka-server</artifactId>
    <version>1.0-SNAPSHOT</version>

    <name>springcloud-eureka-server</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.7</maven.compiler.source>
        <maven.compiler.target>1.7</maven.compiler.target>
    </properties>

    <!--引入springboot-parent父项目-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.7.RELEASE</version>
    </parent>

    <dependencies>
        <!--引入springcloud的euekea server依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
    </dependencies>


    <!--指定下载源和使用springcloud的版本-->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Edgware.SR5</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```



创建如下文件夹

![](https://resource.lzyan.fun/PigGo/20211105145210.png)

配置application.yml文件

```yaml
server:
  port: 8700 # 端口自己决定
  
# 指定当前eureka客户端的注册地址，也就是eureka服务的提供方，当前配置的服务的注册服务方
eureka:
  client:
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka
    register-with-eureka: false #自身 不在向eureka注册
    fetch-registry: false #启动时禁用client的注册
  instance:
    hostname: localhost
 
#指定应用名称
spring:
  application:
    name: eureka-server
```

编写Springboot入口类

![](https://resource.lzyan.fun/PigGo/20211105150418.png)

启动项目后，打开管理界面

![](https://resource.lzyan.fun/PigGo/20211105150402.png)

**客户端client，提供真正服务的角色配置**，它提供服务在服务注册方server（注册中心）进行注册

## 搭建Eureka客户端

同样是新建module，选择quickstart

![](https://resource.lzyan.fun/PigGo/20211105140649.png)

![](https://resource.lzyan.fun/PigGo/20211105151705.png)

配置 `servicesupport`的pom，与server的pom配置相同，把**server**改为**client**

跟第一个微服务同理，需要配置入口类 `pom.xml`， `application.yml`，因为是服务提供者所以还需要提供服务 `controller`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.lzyan</groupId>
    <artifactId>springcloud-eureka-servicesupport</artifactId>
    <version>1.0-SNAPSHOT</version>


    <name>springcloud-eureka-servicesupport</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.7</maven.compiler.source>
        <maven.compiler.target>1.7</maven.compiler.target>
    </properties>

    <!--引入springboot-parent父项目-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.7.RELEASE</version>
    </parent>

    <dependencies>
        <!--引入springcloud的euekea server依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

    </dependencies>


    <!--指定下载源和使用springcloud的版本-->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Edgware.SR5</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

```yaml
server:
  port: 8701 # 服务提供方
 
# 指定当前eureka客户端的注册地址,
eureka:
  client:
    service-url:
      defaultZone: http://${eureka.instance.hostname}:8700/eureka
  instance:
    hostname: localhost
 
#当前服务名称
spring:
  application:
    name: eureka-service
```

```java
@SpringBootApplication
@EnableDiscoveryClient //代表自己是服务提供一方
public class EurekaServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServiceApplication.class, args);
    }
}
```

```java
@RestController
@RequestMapping("/Hello")
public class Controller {
    @RequestMapping("/World")
    public String helloWorld(String s) {
        System.out.println("传入的值:" + s);
        return "传入的值：" + s;
    }

}
```

![](https://resource.lzyan.fun/PigGo/20211106100758.png)

然后启动该服务，启动该服务之前记得先启动server服务

此时进去 http://localhost:8700 界面，就可以看到**服务提供者**已经被注册进**服务注册者**

![](https://resource.lzyan.fun/PigGo/20211106101533.png)

访问**服务提供者**提供的接口 http://localhost:8701/Hello/World?s=lzy

![](https://resource.lzyan.fun/PigGo/20211106101713.png)

**注意：**

一般是不会直接调用所需的微服务，而是经过**提供注册服务**的**server**，获取所需要的**服务提供者列表**（一个列表，此列表包含了能提供相应服务的服务器），他们也许是个集群，因此server会返回一个ip+端口号的表，服务消费者通过相应的算法访问这表上的不同服务器，这些服务器提供的是相同的服务，这种在服务消费者一方挑选服务器为自己服务的方式是一种客户端负载均衡

以**轮询**和**随机**这两种方式访问这些服务器为例，轮询就是循环的意思，假如有3台服务器，访问方式就是1,2,3,1,2,3,1,2,3......，随机就是随机。两种方式都是为了访问每个服务器的可能性尽量相同。还有权重负载这种算法，意思就是根据服务器负载能力分配相应的服务。也就是能力大的干得多，能力少的干得少


## 搭建Eureka注册中心集群

 >由于所有服务都会注册到注册中心去，服务之间的调用都是通过从注册中心获取的服务列表来调用，注册中心一旦宕机，所有服务调用都会出现问题。所以我们需要多个注册中心组成集群来提供服务，下面将搭建一个双节点的注册中心集群。

### 搭建两个注册中心
给 `eureka-sever`添加配置文件 `application-replica1.yml` 配置第一个注册中心
```yaml
server:
  port: 8801
spring:
  application:
    name: eureka-server
eureka:
  instance:
    hostname: replica1
  client:
    serviceUrl:
      defaultZone: http://replica2:8802/eureka/ #注册到另一个Eureka注册中心
    fetch-registry: true
    register-with-eureka: true
```

给 `eureka-sever` 添加配置文件 `application-replica2.yml` 配置第二个注册中心
```yaml
server:
  port: 8802
spring:
  application:
    name: eureka-server
eureka:
  instance:
    hostname: replica2
  client:
    serviceUrl:
      defaultZone: http://replica1:8801/eureka/ #注册到另一个Eureka注册中心
    fetch-registry: true
    register-with-eureka: true
```

通过两个注册中心互相注册，搭建了注册中心的双节点集群，由于 `defaultZone` 使用了域名，所以还需在本机的 `hosts` 文件中配置一下(如果配置不了 `hosts` 文件注意修改权限)
```
127.0.0.1 replica1
127.0.0.1 replica2
```

### 运行Eureka注册中心集群

添加两个配置，分别以 `application-relica1.yml` 和 `application-replica2.yml` 启动euraka-serve

从原启动配置中复制出来两个，并配置
![](https://resource.lzyan.fun/PigGo/20211112173206.png)

启动两个 `eureka-server` ，然后访问其中一个注册中心http://replica1:8801 就会看到另一个已经成为其备份

![](https://resource.lzyan.fun/PigGo/20211112173427.png)

修改Eureka-client，让其连接到集群
添加 `application-replica.yml` ，让其同时注册到两个注册中心去
```yaml
server:
  port: 8812
spring:
  application:
    name: eureka-client
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://replica1:8801/eureka/,http://replica2:8802/eureka/ #同时注册到两个注册中心
```

然后以该配置文件启动后访问任意一个注册中心都可以看到 `eureka-client` 被注册
![](https://resource.lzyan.fun/PigGo/20211112174308.png)

## Eureka注册中心添加认证

创建一个 `eureka-security-server` 模块，`pom.xml` 文件中添加依赖
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```
配置文件 `application.yml` 中添加 `Eureka` 注册中心的配置
```yaml
server:
  port: 8813
spring:
  application:
    name: eureka-security-server
  security: #配置SpringSecurity登录用户名和密码
    user:
      name: lzyan
      password: 123456
eureka:
  instance:
    hostname: localhost
  client:
    fetch-registry: false
    register-with-eureka: false

```
配置 `WebSecurityConfig`

注:默认情况下添加 `SpringSecurity` 依赖的应用每个请求都需要添加 `CSRF token` 才能访问，Eureka 客户端注册时并不会添加，所以需要配置 `/eureka/**` 路径不需要 `CSRF token`。

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().ignoringAntMatchers("/eureka/**");
        super.configure(http);
    }
}
```

注:启动类记得添加 `@EnableEurekaServer` 注解

运行 `eureka-security-server`，访问 http://localhost:8813 ，就会需要进行认证
![](https://resource.lzyan.fun/PigGo/20211112204215.png)

eureka-client注册到有登录认证的注册中心
添加 `application-security.yml` 配置文件，按格式修改用户名和密码
```yaml
server:
  port: 8814
spring:
  application:
    name: eureka-client
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://lzyan:123456@localhost:8813/eureka/
```
其中配置文件修改配置中心地址格式
```
http://${username}:${password}@${hostname}:${port}/eureka/
```

以 `application-security.yml` 配置运行 `eureka-client`，可以在注册中心界面看到 `eureka-client` 已经成功注册
![](https://resource.lzyan.fun/PigGo/20211112213427.png)


## Eureka常用配置
```yaml
eureka:
  client: #eureka客户端配置
    register-with-eureka: true #是否将自己注册到eureka服务端上去
    fetch-registry: true #是否获取eureka服务端上注册的服务列表
    service-url:
      defaultZone: http://localhost:8001/eureka/ # 指定注册中心地址
    enabled: true # 启用eureka客户端
    registry-fetch-interval-seconds: 30 #定义去eureka服务端获取服务列表的时间间隔
  instance: #eureka客户端实例配置
    lease-renewal-interval-in-seconds: 30 #定义服务多久去注册中心续约
    lease-expiration-duration-in-seconds: 90 #定义服务多久不去续约认为服务失效
    metadata-map:
      zone: jiangsu #所在区域
    hostname: localhost #服务主机名称
    prefer-ip-address: false #是否优先使用ip来作为主机名
  server: #eureka服务端配置
    enable-self-preservation: false #关闭eureka服务端的保护机制
```
