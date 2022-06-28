## 关于 Nacos

> 官方简介：[Nacos](https://nacos.io/zh-cn/docs/what-is-nacos.html)

Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

Nacos 是阿里巴巴的产品，现在是[SpringCloud](https://spring.io/projects/spring-cloud)中的一个组件。相比[Eureka](https://github.com/Netflix/eureka)功能更加丰富，在国内受欢迎程度较高。

## 安装 Nacos

- 先从官网下载 Nacos，这里下载的是 `nacos-server-1.4.1.zip` 文件，下载地址：https://github.com/alibaba/nacos/releases

- 配置 `JAVA_HOME` 环境变量，不然无法运行（作为 Javaer，本地的环境变量肯定都有配置好了，所以如果是在 Linux 下的如果没有配置得记得去配置一下）

- 解压安装包，运行脚本在 `bin` 目录下的 `startup.cmd` （windows）

- Nacos 的默认端口号是 `8848`，可以进入 Nacos 的 conf 目录下的 `application.properties` 文件，修改配置文件中的端口

![](https://resource.lzyan.fun/PigGo/20220519092915.png)

- 运行命令：`startup.cmd -m standalone` 代表单机运行

- 运行成功后，访问 `http://localhost:8848/nacos` 可以查看 Nacos 的主页，默认账号密码都是 nacos

![](https://resource.lzyan.fun/PigGo/20220523102521.png)

## 注册中心

### 项目准备

项目源来自黑马 微服务开发框架教程，项目目录结构如下，就是一个基本的项目结构：springboot+mybatis ，不再赘述构建过程，[项目地址](https://github.com/YCheer/cloud-demo)

![](https://resource.lzyan.fun/PigGo/20220519105117.png)

在 `cloud-demo` 父工程中的 `pom.xml` 文件中添加 `SpringCloudAlibaba` 的依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

在两个服务模块 `user-service` 和 `order-service` 的 `pom.xml` 文件中添加 `nacos-discovery` 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

### 配置 nacos 地址

在 `user-service` 和 `order-service` 的 `application.yml` 文件中添加 `nacos` 的地址

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
```

启动项目后即可发现这两个服务已经注册上去了

![](https://resource.lzyan.fun/PigGo/20220627152830.png)

## 配置中心

当部署的微服务实例越来越多达到一定数量的时候，此时如果要一个一个地修改这些微服务的配置，很明显是不够明智的。而 nacos 一方面可以将这些配置集中管理，另一方面还可以在配置变更的时及时通知微服务，实现配置的热更新。

### nacos中添加配置文件

![](https://resource.lzyan.fun/PigGo/20220627155927.png)

![](https://resource.lzyan.fun/PigGo/20220627160005.png)

> 注意：项目的核心配置，需要热更新的配置才有放到nacos管理的必要。基本不会变更的一些配置还是保存在微服务本地比较好。

### 从微服务拉取配置

微服务要拉取 nacos 中管理的配置，并且与本地的 `application.yml` 配置合并，才能完成项目启动。但如果尚未读取 `application.yml`，又如何得知 nacos 地址呢？

因此 spring 引入了一种新的配置文件：`bootstrap.yaml` 文件，会在 `application.yml` 之前被读取，流程如下：

![](https://resource.lzyan.fun/PigGo/L0iFYNF.png)

在 `user-service` 服务中，引入 `nacos-config` 的客户端依赖：

```xml
<!--nacos配置管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

在 `user-service` 中添加一个 `bootstrap.yaml` 文件，内容如下：

```yaml
spring:
  application:
    name: userservice # 服务名称
  profiles:
    active: dev #开发环境，这里是dev 
  cloud:
    nacos:
      server-addr: localhost:8848 # Nacos地址
      config:
        file-extension: yaml # 文件后缀名
```

这里会根据spring.cloud.nacos.server-addr获取nacos地址，再根据

`${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}`作为文件id，来读取配置。

本例中，就是去读取`userservice-dev.yaml`：

![](https://resource.lzyan.fun/PigGo/image-20210714170845901.png)

在` user-service` 中的 `UserController` 中添加业务逻辑，读取 `pattern.dateformat` 配置：

![](https://resource.lzyan.fun/PigGo/20220627161427.png)

调用测试

![](https://resource.lzyan.fun/PigGo/image-20210714170449612.png)


### 配置热更新

当修改 nacos 中的配置后，微服务中无需重启即可让配置生效，也就是**配置热更新**，要实现配置热更新，有两种方式。

- 方式一：

在 `@Value` 注入的变量所在类上添加 `@RefreshScope` 注解：

![](https://resource.lzyan.fun/PigGo/20220627163352.png)

- 方式二：

使用 `@ConfigurationProperties` 注解代替 `@Value` 注解。

在 `user-service` 服务中，添加一个类，读取 `patterrn.dateformat` 属性：

```java
package cn.itcast.user.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/**
 * @author lzyan
 * @description
 */
@Component
@Data
@ConfigurationProperties(prefix = "pattern")
public class PatternProperties {
    private String dateformat;
}

```

![](https://resource.lzyan.fun/PigGo/20220627164756.png)

