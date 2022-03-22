### kafka 是什么

官方定义 kafka 是一个分布式流式计算平台。[官方详细的介绍](https://kafka.apache.org/intro)

流平台具有三个关键功能：

1. **消息队列**：发布和订阅消息流，这个功能类似于消息队列，这也是 Kafka 也被归类为消息队列的原因。

2. **容错的持久方式存储记录消息流**： Kafka 会把消息持久化到磁盘，有效避免了消息丢失的风险。

3. **流式处理平台**： 在消息发布的时候进行处理，Kafka 提供了一个完整的流式处理类库。

Kafka 主要有两大应用场景：

1. **消息队列** ：建立实时流数据管道，以可靠地在系统或应用程序之间获取数据。

2. **数据处理**： 构建实时的流数据处理程序来转换或处理数据流。


### 传统消息队列的两种模式

- **点对点模式（一对一，消费者主动拉取数据，消息收到后消息清除）**

消息生产者生产消息发送到 Queue 中，然后消息消费者冲 Queue 中取出并且消费消息。消息被消费以后，Queue中不再存储，所以消息消费者不可能消费到已经被消费的消息。Queue 支持存在多个消费者，但是对一个消息而言，只会有一个消费者可以消费，未被消费的消息在队列中保留直到被消费或超时。

![](https://resource.lzyan.fun/PigGo/20220319102755.png)

- **发布/订阅模式（一对多，数据生产后，推送给所有订阅者）**

消息生产者（发布）将消息发布到 topic 中，同时有多个消息消费者（订阅）消费该消息。和点对点方式不同，发布到 topic 的消息会被所有订阅者消费。在发布/订阅模型中，如果只有一个订阅者，那它和队列模型就基本是一样的了。所以说，发布/订阅模型在功能层面上是可以兼容队列模型的。

Kafka 采用的就是发布/订阅模型，RocketMQ 的消息模型和 Kafka 基本是完全一样的。唯一的区别是 Kafka 中没有队列这个概念，与之对应的是 Partition（分区）。

![](https://resource.lzyan.fun/PigGo/20220319102849.png)

###  Kafka 基础架构图

![](https://resource.lzyan.fun/PigGo/20220319142259.png)

- **Producer** ：消息生产者，就是向 kafka broker 发消息的客户端；

- **Consumer** ：消息消费者，向 kafka broker 取消息的客户端；

- **Consumer Group （CG）**：消费者组，由多个 consumer 组成。消费者组内每个消费者负责消费不同分区的数据，一个分区只能由一个组内消费者消费；消费者组之间互不影响。所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。

- **Broker** ：一台 kafka 服务器就是一个 broker（虽然多个 Broker 进程能够运行在同一台机器上，但更常见的做法是将不同的 Broker 分散运行在不同的机器上）。一个集群由多个 broker 组成。一个 broker 可以容纳多个 topic；

- **Topic** ：可以理解为一个队列，Kafka 的消息通过 Topics(主题) 进行分类，生产者和消费者面向的都是一个 topic；

- **Partition**：为了实现扩展性，一个非常大的 topic 可以分布到多个 broker（即服务器）上， 一个 topic 可以分为多个 partition，每个 partition 是一个有序的队列； partition 中的每条消息都会被分配一个有序的 id（ offset）。 kafka 只保证按一个 partition 中的顺序将消息发给 consumer，不保证一个 topic 的整体（多个 partition 间）的顺序；

- **Replica**：副本，为保证集群中的某个节点发生故障时，该节点上的 partition 数据不丢失，且 kafka 仍然能够继续工作，kafka 提供了副本机制，一个 topic 的每个分区都有若干个副本， 一个 leader 和若干个 follower；

- **leader**：每个分区多个副本的“主”，生产者发送数据的对象，以及消费者消费数据的对象都是 leader；

- **follower**：每个分区多个副本中的“从”，实时从 leader 中同步数据，保持和 leader 数据的同步。leader 发生故障时，某个 follower 会成为新的 follower；

- **Offset**： kafka 的存储文件都是按照 offset.kafka 来命名，用 offset 做名字的好处是方便查找。例如你想找位于 2049 的位置，只要找到 2048.kafka 的文件即可。当然 the first offset 就是 00000000000.kafka。

### kafka 和 zookeeper 的关系

1. **Broker 注册**：在 Zookeeper 上会有一个专门用来进行 Broker 服务器列表记录的节点。每个 Broker 在启动时，都会到 Zookeeper 上进行注册，即到 `/brokers/ids` 下创建属于自己的节点。每个 Broker 就会将自己的 IP 地址和端口等信息记录到该节点中去。

2. **Topic 注册** ： 在 Kafka 中，同一个Topic 的消息会被分成多个分区并将其分布在多个 Broker 上，这些分区信息及与 Broker 的对应关系也都是由 Zookeeper 在维护。比如我创建了一个名字为 my-topic 的主题并且它有两个分区，对应到 zookeeper 中会创建这些文件夹：`/brokers/topics/my-topic/Partitions/0`、`/brokers/topics/my-topic/Partitions/1`。分区 partition 是多副本保存的，当 leader 分区所在的 broker 发生故障时，partition 需要重新选举 leader，这个需要由 Zookeeper 类主导完成。

3. **consumer 注册**：消费者组也会向 Zookeeper 进行注册，Zookeeper 会为其分配节点来保存相关数据，节点路径为 `/consumers/{group_id}`，有3个子节点。如下图：

![](https://resource.lzyan.fun/PigGo/20220319152522.png)

这样 Zookeeper 可以记录分区跟消费者的关系，以及分区的 offset。

4. **负载均衡**：Kafka 通过给特定 Topic 指定多个 Partition, 而各个 Partition 可以分布在不同的 Broker 上, 这样便能提供比较好的并发能力。 对于同一个 Topic 的不同 Partition，Kafka 会尽力将这些 Partition 分布到不同的 Broker 服务器上。当生产者产生消息后也会尽量投递到不同 Broker 的 Partition 里面。当 Consumer 消费的时候，Zookeeper 可以根据当前的 Partition 数量以及 Consumer 数量来实现动态负载均衡。

部署 Kafka 就必须得部署 Zookeeper ，但 Kafka 未来的 2.8 版本将要放弃 Zookeeper。[Kafka为什么要放弃Zookeeper](https://zhuanlan.zhihu.com/p/367185578)

### 如何保证 kafka 消息的消费顺序
### 如何保证 kafka 消息不丢失


### 动手体验

[5分钟带你体验一把Kafka](https://github.com/Snailclimb/springboot-kafka/blob/master/docs/2-5%E5%88%86%E9%92%9F%E5%B8%A6%E4%BD%A0%E4%BD%93%E9%AA%8C%E4%B8%80%E6%8A%8AKafka.md)

[10分钟学会如何在SpringBoot程序中使用Kafka作为消息队列](https://github.com/Snailclimb/springboot-kafka/blob/master/docs/3-10%E5%88%86%E9%92%9F%E5%AD%A6%E4%BC%9A%E5%A6%82%E4%BD%95%E5%9C%A8SpringBoot%E7%A8%8B%E5%BA%8F%E4%B8%AD%E4%BD%BF%E7%94%A8Kafka%E4%BD%9C%E4%B8%BA%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97.md)

### 参考

[Kafka知识点&面试题总结](https://javaguide.cn/high-performance/message-queue/kafka-questions-01.html#kafka-%E6%98%AF%E4%BB%80%E4%B9%88-%E4%B8%BB%E8%A6%81%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF%E6%9C%89%E5%93%AA%E4%BA%9B)

[Hello-Kafka](https://javakeeper.starfish.ink/distribution/message-queue/Kafka/Hello-Kafka.html#_1-kafka%E6%A6%82%E8%BF%B0)

[刨根问底，kafka到底会不会丢消息](https://github.com/CoderLeixiaoshuai/java-eight-part/blob/master/docs/mq/%E5%88%A8%E6%A0%B9%E9%97%AE%E5%BA%95%EF%BC%8Ckafka%E5%88%B0%E5%BA%95%E4%BC%9A%E4%B8%8D%E4%BC%9A%E4%B8%A2%E6%B6%88%E6%81%AF.md)