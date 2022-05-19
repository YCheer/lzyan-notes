## 关于 Nacos

> 官方简介：[Nacos](https://nacos.io/zh-cn/docs/what-is-nacos.html)

Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

Nacos 是阿里巴巴的产品，现在是[SpringCloud](https://spring.io/projects/spring-cloud)中的一个组件。相比[Eureka](https://github.com/Netflix/eureka)功能更加丰富，在国内受欢迎程度较高。

## 安装 Nacos

- 先从官网下载 Nacos，这里下载的是 `nacos-server-1.4.1.zip` 文件，下载地址：https://github.com/alibaba/nacos/releases

- 配置 `JAVA_HOME` 环境变量，不然无法运行（作为Javaer，本地的环境变量肯定都有配置好了，所以如果是在Linux下的如果没有配置得记得去配置一下）

- 解压安装包，运行脚本在 `bin` 目录下的 `startup.cmd` （windows）

- Nacos 的默认端口号是 `8848`，可以进入 Nacos 的 conf 目录下的 `application.properties` 文件，修改配置文件中的端口

![](https://resource.lzyan.fun/PigGo/20220519092915.png)

- 运行命令：`startup.cmd -m standalone` 代表单机运行

- 运行成功后，访问 `http://localhost:8848/nacos` 可以查看 Nacos 的主页，默认账号密码都是 nacos

![](https://resource.lzyan.fun/PigGo/20220519093315.png)

## Nacos 做注册中心

