### 3.服务的调用方式

第一种调用方式：restTemplate+ribbon

第二种调用方式：feign

**restTemplate+ribbon**

**Ribbon** 是 **Netflix** 公司的一个开源的负载均衡项目，是一个客户端/进程内负载均衡器，**运行在消费者端**，详情文

[Ribbon详解]: https://www.jianshu.com/p/1bd66db5dc46	"Ribbon"

> 而**客户端负载均衡**和**服务端负载均衡**最大的不同点在于上面所提到**服务清单所存储的位置**。在客户端负载均衡中，所有客户端节点都维护着自己要访问的服务端清单，而这些服务端端清单**来自于服务注册中心**，比如上一章我们介绍的Eureka服务端。
>
> 同服务端负载均衡的架构类似，在客户端负载均衡中也**需要心跳去维护服务端清单**的健康性，默认会创建针对各个服务治理框架的Ribbon自动化整合配置，比如Eureka中的org.springframework.cloud.netflix.ribbon.eureka.RibbonEurekaAutoConfiguration，Consul中的org.springframework.cloud.consul.discovery.RibbonConsulAutoConfiguration。在实际使用的时候，我们可以通过查看这两个类的实现，以找到它们的配置详情来帮助我们更好地使用它。

搭建基于**Ribbon**的客户端，用于消费服务

同理与如上搭建的servicesupport不同的是

pom文件中添加ribbon依赖

```xml
<dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-ribbon</artifactId>
</dependency>
```

yml文件配置，服务的消费方依旧需要在注册方8700端口去注册。配置当前服务消费方的端口8072，名字为eureka-consumer

```yaml
server:
  port: 8702 # 服务消费方
 
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
    name: eureka-consumer
```

启动类

```java
@SpringBootApplication
@EnableDiscoveryClient //当前使用eureka的server
public class EurekaConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaConsumerApplication.class,args);
    }
}
```

controller类编写ribbon实现

```java
@RestController
@RequestMapping("/Hello")
class ConsumerController {
    @Autowired
    private LoadBalancerClient loadBalancerClient;
    @Autowired
    private RestTemplate restTemplate;
    @RequestMapping("/Consumer")
    public String helloWorld(String s){
        System.out.println("传入的值为："+s);
        //第一种调用方式
        //String forObject = new RestTemplate().getForObject("http://localhost:8071/Hello/World?s=" + s, String.class);
 
        //第二种调用方式
        //根据服务名 获取服务列表 根据算法选取某个服务 并访问某个服务的网络位置。
        //ServiceInstance serviceInstance = loadBalancerClient.choose("EUREKA-SERVICE");
        //String forObject = new RestTemplate().getForObject("http://"+serviceInstance.getHost()+":"+serviceInstance.getPort()+"/Hello/World?s="+s,String.class);
 
        //第三种调用方式 需要restTemplate注入的方式
        String forObject = restTemplate.getForObject("http://EUREKA-SERVICE/Hello/World?s=" + s, String.class);
        return forObject;
    }
}
```

注入restTemplate

```java
@Configuration
public class Beans {
    //管理简单对象
    @Bean //告诉工厂，这个方法需要自动注入
    @LoadBalanced //表示需要做负载均衡
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
```

这里有三种调用方式

1. 直接调用：不经过注册中心拿服务列表，直接访问servicesupport
2. 选择调用：根据服务名选择调用，需要注入LoadBalancerClient，用服务名去注册中心获取服务列表，当前客户端底层会做随机算法的选取获得服务并访问
3. restTemplate对象调用：需要一个@Bean的注解自动注入并直接调用restTemplate对象调用服务。底层调用模式与第二种方式一样

![](https://resource.lzyan.fun/PigGo/20211106113356.png)

启动测试：依次启动springcloud-euraka-server --> springcloud-eureka-servicesupport --> springcloud-eureka-serviceconsumer

![](https://resource.lzyan.fun/PigGo/20211106114024.png)

访问过程解析：

![](https://resource.lzyan.fun/PigGo/20211106114431.png)







