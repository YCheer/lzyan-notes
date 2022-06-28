## 服务远程调用

### 项目准备

项目源来自黑马 微服务开发框架教程，项目目录结构如下，就是一个基本的项目结构:springboot+mybatis，不再赘述构建过程,[项目地址](https://github.com/YCheer/cloud-demo)

![](https://resource.lzyan.fun/PigGo/20220519105117.png)

在 `order-service` 服务中，有一个根据 id 查询订单的接口：

```java
@RestController
@RequestMapping("order")
public class OrderController {

   @Autowired
   private OrderService orderService;

    @GetMapping("{orderId}")
    public Order queryOrderByUserId(@PathVariable("orderId") Long orderId) {
        // 根据id查询订单并返回
        return orderService.queryOrderById(orderId);
    }
}
```

在 `user-service` 中有一个根据 id 查询用户的接口：

```java
@Slf4j
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    /**
     * 路径： /user/110
     *
     * @param id 用户id
     * @return 用户
     */
    @GetMapping("/{id}")
    public User queryById(@PathVariable("id") Long id) {
        return userService.queryById(id);
    }
}
```

### 使用 RestTemplate 实现远程调用

首先在 `order-service` 服务中的 `OrderApplication` 启动类中，注册 `RestTemplate` 实例：

```java
@MapperScan("cn.itcast.order.mapper")
@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

然后修改 `order-service` 服务中的 `cn.itcast.order.service` 包下的 `OrderService` 类中的 `queryOrderById` 方法：

```java
@Service
public class OrderService {

    @Autowired
    private OrderMapper orderMapper;

    @Autowired
    private RestTemplate restTemplate;

    public Order queryOrderById(Long orderId) {
        // 1.查询订单
        Order order = orderMapper.findById(orderId);
        // 2.远程查询 user
        // 2.1 url地址
        String url = "http://localhost:8081/user/" + order.getUserId();
        User user = restTemplate.getForObject(url, User.class);
        // 3. 存入order
        order.setUser(user);
        // 4.返回
        return order;
    }
}
```

修改完成，启动这两个服务调用测试

![](https://resource.lzyan.fun/PigGo/20220519111333.png)

![](https://resource.lzyan.fun/PigGo/20220519111514.png)

## Ribbon 负载均衡

### 负载均衡实现

这里结合 `Eureka` 来使用 `Ribbon` 负载均衡，关于 `Eureka` 的集成参考系列中的 `SpringCloud Eureka：服务注册于发现`。具体的实现方式其实很简单，无须赘述。当集成了 `Eureka` 后也就相当于集成了 `Ribbon` ，可以看到

![](https://resource.lzyan.fun/PigGo/20220519125727.png)

只需要在服务调用方的 `RestTemplate` 里面加上 `@LoadBalanced` 负载均衡注解即可

![](https://resource.lzyan.fun/PigGo/20220519130048.png)

然后修改一下远程调用的 `url` ，用服务名代替 ip、端口。此处我的 `user-service` 这个服务通过集成 `Eureka` 后配置命名为 `userservice`

![](https://resource.lzyan.fun/PigGo/20220519130637.png)

### 负载均衡测试

启动两个不同端口的 `user-service` 服务

![](https://resource.lzyan.fun/PigGo/20220519131400.png)

![](https://resource.lzyan.fun/PigGo/20220519131439.png)

此时这些服务都在 `Eureka` 下进行注册了， 调用接口则会发现 `8081` 和 `8082` 端口的服务在交替地被调用

### 负载均衡原理

在 `RestTemplate` 对象加上 `LoadBalanced` 注解就是作了一个标记，标记着 `RestTemplate` 对象发起的 `Http` 请求要被拦截和处理。是 `LoadBalancerInterceptor` 这个对象来处理的，查看源码

![](https://resource.lzyan.fun/PigGo/20220519200522.png)

标红的两处，其中实现了 `ClientHttpRequestInterceptor` 这个接口并且重写了 `intercept` 方法，意味着就是能拦截客户端 HTTP 请求

![](https://resource.lzyan.fun/PigGo/20220519205331.png)

在 `intercept` 这个方法中打个断点继续查看

![](https://resource.lzyan.fun/PigGo/20220519205833.png)

可以看到这里的 `intercept` 方法，拦截了用户的 `HttpRequest` 请求，然后做了几件事：

- `request.getURI()`：获取请求 uri，也就是图中的 `http://user-service/user/8`
- `originalUri.getHost()`：获取 uri 路径的主机名，其实就是服务 id，`user-service`
- `this.loadBalancer.execute()`：处理服务 id 和用户的请求

继续跟进 `execute()` 方法

![](https://resource.lzyan.fun/PigGo/20220519210625.png)

- `getLoadBalancer(serviceId)`：根据服务 id 获取 ILoadBalancer ，而 ILoadBalancer 会拿着服务 id 去 eureka 中获取服务列表并保存起来。
- `getServer(loadBalancer)`：利用内置的负载均衡算法，从服务列表中选择一个。本例中，可以看到获取了 8082 端口的服务

放行后，再次访问并跟踪，发现获取的是 8081，所以说这是负载均衡的

![](https://resource.lzyan.fun/PigGo/20220519211057.png)

回到刚才的代码中，可以看到获取服务是通过一个 `getService` 方法来做负载均衡的

![](https://resource.lzyan.fun/PigGo/20220519211755.png)

进入 `chooseServer` 方法，发现有这么一段代码，在选择规则

![](https://resource.lzyan.fun/PigGo/20220519213126.png)

于是查看一下这个 `rule` 是什么来头，可以看到它默认是一个 `RoundRobinRule`，于是从字面意思也就能猜到这是什么了

![](https://resource.lzyan.fun/PigGo/20220519213236.png)

进去 `RoundrobinRule` 这个类看看，写着 轮询策略

![](https://resource.lzyan.fun/PigGo/20220519213351.png)

总结：`SpringCloudRibbon` 的底层采用了一个拦截器，拦截了 `RestTemplate` 发出的请求，对地址做了修改。

![](https://resource.lzyan.fun/PigGo/20220519213724.png)

## 负载均衡策略

### IRule 接口

`Ribbon` 的负载均衡规则是一个叫做 `IRule` 的接口来定义的，每一个子接口都是一种规则，默认的实现就是 `ZoneAvoidanceRule`，是一种轮询方案

![](https://resource.lzyan.fun/PigGo/20220519214510.png)

不同规则的含义：

| **内置负载均衡规则类**    | **规则描述**                                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RoundRobinRule            | 简单轮询服务列表来选择服务器。它是 Ribbon 默认的负载均衡规则。                                                                                                                                                                                                                                                                                                                                                       |
| AvailabilityFilteringRule | 对以下两种服务器进行忽略： （1）在默认情况下，这台服务器如果 3 次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续 30 秒，如果再次连接失败，短路的持续时间就会几何级地增加。 （2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置了 AvailabilityFilteringRule 规则的客户端也会将其忽略。并发连接数的上限，可以由客户端的<clientName>.<clientConfigNameSpace>.ActiveConnectionsLimit 属性进行配置。 |
| WeightedResponseTimeRule  | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择。                                                                                                                                                                                                                                                                                   |
| **ZoneAvoidanceRule**     | 以区域可用的服务器为基础进行服务器的选择。使用 Zone 对服务器进行分类，这个 Zone 可以理解为一个机房、一个机架等。而后再对 Zone 内的多个服务做轮询。                                                                                                                                                                                                                                                                   |
| BestAvailableRule         | 忽略那些短路的服务器，并选择并发数较低的服务器。                                                                                                                                                                                                                                                                                                                                                                     |
| RandomRule                | 随机选择一个可用的服务器。                                                                                                                                                                                                                                                                                                                                                                                           |
| RetryRule                 | 重试机制的选择逻辑                                                                                                                                                                                                                                                                                                                                                                                                   |

### 指定负载均衡规则

方式一：服务消费者的代码实现指定 `IRule` 的实现类（注意：此方式作用范围是针对消费者中单一的服务提供者）

```java
    @Bean
    public IRule rule(){
        return new RandomRule();
    }
```

方式二：服务消费者的配置文件 `application.yml` 中配置负载均衡规则（注意：此方式作用范围是针对消费者中单一的服务提供者）

```yaml
userservice: # 给某个微服务配置负载均衡规则，这里是userservice服务
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RoundRobinRule # 负载均衡规则
```

### 饥饿加载

`Ribbon` 默认是采用懒加载，即第一次访问时才会去创建 `LoadBalanceClient` ，请求时间会很长。而饥饿加载则会在项目启动时创建，降低第一次访问的耗时，通过下面配置开启饥饿加载：

```yaml
ribbon:
  eager-load:
    enabled: true # 开启饥饿加载
    clients: # 指定服务名为userservice的服务饥饿加载，这是一个List，可通过这种方式指定多个
      - userservice
      - userservice1
```

## Ribbon 常用配置总结

### 全局配置

```yaml
ribbon:
  ConnectTimeout: 1000 #服务请求连接超时时间（毫秒）
  ReadTimeout: 3000 #服务请求处理超时时间（毫秒）
  OkToRetryOnAllOperations: true #对超时请求启用重试机制
  MaxAutoRetriesNextServer: 1 #切换重试实例的最大个数
  MaxAutoRetries: 1 # 切换实例后重试最大次数
  NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule #修改负载均衡算法
```

### 指定服务配置

与全局配置的区别就是 ribbon 节点挂在服务 id 下面

```yaml
userservice:
  ribbon:
    ConnectTimeout: 1000 #服务请求连接超时时间（毫秒）
    ReadTimeout: 3000 #服务请求处理超时时间（毫秒）
    OkToRetryOnAllOperations: true #对超时请求启用重试机制
    MaxAutoRetriesNextServer: 1 #切换重试实例的最大个数
    MaxAutoRetries: 1 # 切换实例后重试最大次数
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule #修改负载均衡算法
```
