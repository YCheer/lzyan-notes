![D2EVjnwWoAAV74u.jpg](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/jpg/832926954255548416.jpg)
## 安装RocketMQ

### 我的环境

这是单机搭建的，搭在了华为云的HECS上

1. 系统环境：Centos7.6 64bit
2. jdk：1.8
3. Maven：3.6.3
4. Git

### 部署RocketMQ

这里直接进入主题安装RocketMQ，其他环境这里先不提了。RocketMQ官网：http://rocketmq.apache.org/

先用git把源码给拉下来

```
git clone https://github.com/apache/rocketmq.git
```

然后用maven打包编译这份源码，这个过程有点漫长...华为云HECS 1核2G用了20分钟左右

```
cd rocketmq
mvn -Prelease-all -DskipTests clean install -U
```

编译完成后可以把编译好的压缩包复制到别的目录下

```
# 我这里复制到 /home/rocketMQ
cp distribution/target/rocketmq-4.7.1.tar.gz /home/rocketMQ
```

解压打包好的文件

```
tar zxvf rocketmq-4.7.1.tar.gz 
```

配置环境变量，其实可以不设置，但是配置了可以进一步简化broker的集群命令

```
vim /etc/profile
# 在末尾添加，其中/home/rocketMQ/rocketmq-4.7.1是你解压后文件的所在地
export ROCKETMQ_HOME=/home/rocketMQ/rocketmq-4.7.1
export PATH=$JAVA_HOME/bin:$ROCKETMQ_HOME/bin:$PATH
#使配置文件生效
source /etc/profile
```

这里由于官方有个硬性要求，要求内存4g+，而我的辣鸡主机也就2g，得修改一下配置才可以启动，否则会有内存分配不够的问题

```
# 去到bin目录下，runserver.sh、runbroker.sh这两个文件就是即将要改的
cd bin

#修改第一个文件
vim runserver.sh 
#找到
JAVA_OPT="${JAVA_OPT} -server -Xms8g -Xmx8g -Xmn4g -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
#自定义配置成
JAVA_OPT="${JAVA_OPT} -server -Xms512m -Xmx512m -Xmn256m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"

修改第二个文件 同理
vim runbroker.sh
```

![image-20200731130151622.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927060245610496.png)

在启动之前先去配置一下broker的远程ip

```
# 可以查看broker的配置
sh ./bin/mqbroker -m
# 将公网IP写入配置文件中，也可以用vim来，我这里直接echo进去了
echo 'brokerIP1=xxx.xxx.xxx.xxx' > conf/broker.conf
```

别忘了开在华为云控制台开启端口（阿里云也一样）

![image-20200801124814022.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927133981474816.png)

启动name server

```
#启动
nohup sh mqnamesrv &
#看log
tail -f /root/logs/rocketmqlogs/namesrv.log
```

![image-20200731132844697.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927238067322880.png)

启动broker

```
# 启动 autoCreateTopicEnable开启就会自动创建主题不然就需要手动创建了
nohup sh mqbroker -n xxx.xxx.xxx.xxx:9876 -c broker.conf autoCreateTopicEnable=true &
#看log
tail -f ~/logs/rocketmqlogs/broker.log
```

![image-20200801124305830.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927342971060224.png)
关闭服务

```
sh mqshutdown broker
sh mqshutdown namesrv
```

### 安装可视化管理面板

有一个对RocketMQ进行扩展的开源项目incubator-rocketmq-externals，这个项目中有一个子模块叫rocketmq-console，这个便是管理控制台项目

这里先把代码拉下来（可以装在自己本地主机就行）

```
git clone https://github.com/apache/rocketmq-externals.git
```

编译打包

```
cd rocketmq-externals/rocketmq-console
mvn clean package -Dmaven.test.skip=true
```

然后启动

```
cd target/
java -jar rocketmq-console-ng-2.0.0.jar --server.port=8080 --rocketmq.config.namesrvAddr=xxx.xxx.xxx.xxx（公网ip）:9876
```

打开浏览器访问

![image-20200731173150134.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927418808270848.png)

## 开始实战

### 实战内容

实现生产者（producer）写入消息和消费者（consumer）消费消息

![image-20200731121737118.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927480900747264.png)

### 创建两个项目，对应生产者和消费者

项目1：rocketmq-producer，项目2：consumer-producer，都只添加一个web依赖就可以了

![image-20200731173042866.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927544507367424.png)

### 为两个项目添加坐标导入jar包

```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-spring-boot-starter</artifactId>
    <version>2.1.0</version>
</dependency>
```

### 生产者代码编写

首先是添加配置，我改成了yml的形式了

```yaml
rocketmq:
  name-server: xxx.xxx.xxx.xxx:9876  #公网ip
  producer:
    group: my_group
```

添加一个User类

```java
public class User {
    private String userName;
    private Byte userAge;
	
	// setter getter 略

    @Override
    public String toString() {
        return "User{" +
                "userName='" + userName + '\'' +
                ", userAge=" + userAge +
                '}';
    }
}
```

写入消息

```java
@Service
public class ProducerService {
    private Logger logger = LoggerFactory.getLogger(getClass());

    @Resource
    private RocketMQTemplate mqTemplate;

    private String springTopic = "string-topic";

    private String userTopic ="user-topic";

    public SendResult sendString(String message) {
        // 发送 String 类型的消息
        // 调用 RocketMQTemplate 的 syncSend 方法
        SendResult sendResult = mqTemplate.syncSend(springTopic, message);
        logger.info("syncSend String to topic {} sendResult={} \n", springTopic, sendResult);
        return sendResult;
    }

    public SendResult sendUser(User user) {
        // 发送 User
        SendResult sendResult = mqTemplate.syncSend(userTopic, user);
        logger.info("syncSend User to topic {} sendResult= {} \n", userTopic, sendResult);
        return sendResult;
    }
}
```

提供两个Restful API

```java
@RestController
@RequestMapping("/producer")
public class ProducerController {
    @Resource
    ProducerService producerService;

    @PostMapping("/string")
    public SendResult sendString(@RequestBody String message){
        return producerService.sendString(message);
    }

    @PostMapping("/user")
    public SendResult sendUser(@RequestBody User user){
        return producerService.sendUser(user);
    }
}
```

### 消费者代码编写

添加配置

```yamL
spring:
  main:
    web-application-type: none #不启动web容器
rocketmq:
  name-server: xxx.xxx.xxx.xxx:9876 #公网ip
  producer:
    group: my_group
```

消费topic="string-topic"的消息

```java
@Service
@RocketMQMessageListener(topic = "string-topic", consumerGroup = "string_consumer")
public class StringConsumer implements RocketMQListener<String> {
    private Logger logger = LoggerFactory.getLogger(getClass());

    @Override
    public void onMessage(String message) {
        // 重写消息处理方法
        logger.info("------- StringConsumer received:{} \n", message);
        // TODO：对接收的消息作处理
    }
}
```

消费topic="user-topic"的消息

```java
@Service
@RocketMQMessageListener(topic = "user-topic", consumerGroup = "user_consumer")
public class UserConsumer implements RocketMQListener<User> {
    private Logger logger = LoggerFactory.getLogger(getClass());
    @Override
    public void onMessage(User user) {
        logger.info("######## user_consumer received: {} ; age: {} ; name: {} \n", user,user.getUserAge(),user.getUserName());
        // TODO：对接收的消息作处理
    }
}
```

### 运行项目

生产者

![image-20200801134853435.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927646366040064.png)

消费者

![image-20200801134933858.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927712032063488.png)

### 用postman测试

测试topic="string-topic"的消息

![image-20200801135114690.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927779539386368.png)

测试topic="user-topic"的消息

![image-20200801135432808.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927876570415104.png)

生产者输出

![image-20200801135455900.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832927955322667008.png)

消费者输出

![image-20200801135521507.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832928016718888960.png)

## 遇到的问题

- A component required a bean of type 'org.apache.rocketmq.spring.core.RocketMQTemplate' that could no be found

spring项目启动的时候一直打印远程地址为空

![image-20200801112013715.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832928085446754304.png)

查看broker的启动信息的时候会发现192.168.0.158这个地址，这个地址只是个内网ip来的

![image-20200801112239155.png](http://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832928144074735616.png)

所以问题就是brokerIP是服务器的内网IP导致producer无法建立连接，所以这时候需要去修改conf/broker.conf文件，在前面搭建的时候有步骤



- A component required a bean of type 'org.apache.rocketmq.spring.core.RocketMQTemplate' that could not be found

我的问题是因为项目的配置文件写错了，正确地输入格式如此链接下方描述https://github.com/apache/rocketmq-spring/issues/98#issuecomment-582230155'

```yaml
spring:
    redis:
        database: 0
        host: localhost
        ........
rocketmq:
  name-server: 172.16.21.138:9876
  producer:
    group: short-message
```
