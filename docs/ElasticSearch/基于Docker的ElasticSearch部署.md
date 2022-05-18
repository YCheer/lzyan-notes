## 1. 关于 ElasticSearch 

### 1.1 什么是 ElasticSearch

`ElasticSearch` 是一款非常强大的、基于 `Lucene` 的开源搜索及分析引擎；它是一个实时的分布式搜索分析引擎，它能让你以前所未有的速度和规模，去探索你的数据。

它被用作全文检索、结构化搜索、分析以及这三个功能的组合：

- Wikipedia 使用 Elasticsearch 提供带有高亮片段的全文搜索，还有 search-as-you-type 和 did-you-mean 的建议。 
- 卫报 使用 Elasticsearch 将网络社交数据结合到访客日志中，为它的编辑们提供公众对于新文章的实时反馈。
- Stack Overflow 将地理位置查询融入全文检索中去，并且使用 more-like-this 接口去查找相关的问题和回答。 
- GitHub 使用 Elasticsearch 对1300亿行代码进行查询。 
- ...

除了搜索，结合 **Kibana、Logstash、Beats** 开源产品，**Elastic Stack（简称ELK）** 还被广泛运用在大数据近实时分析领域，包括：**日志分析、指标监控、信息安全**等。它可以帮助你探索 **海量结构化、非结构化数据，按需创建可视化报表，对监控数据设置报警阈值，通过使用机器学习，自动识别异常状况。**

`ElasticSearch` 是基于 `Restful WebApi`，使用Java语言开发的搜索引擎库类，并作为Apache 许可条款下的开放源码发布，是当前流行的企业级搜索引擎。其客户端在Java、C#、PHP、Python等许多语言中都是可用的。

### 1.2 与关系型数据库的概念对比

es 的一些概念：

- **Near Realtime（NRT） 近实时**：数据提交索引后，立马就可以搜索到。 

- **Cluster 集群**：一个集群由一个唯一的名字标识，默认为“elasticsearch”。集群名称非常重要，具有相同集群名的节点才会组成一个集群。集群名称可以在配置文件中指定。 

- **Node 节点**：存储集群的数据，参与集群的索引和搜索功能。像集群有名字，节点也有自己的名称，默认在启动时会以一个随机的UUID的前七个字符作为节点的名字，你可以为其指定任意的名字。通过集群名在网络中发现同伴组成集群。一个节点也可是集群。

- **Index 索引**: 一个索引是一个文档的集合（等同于solr中的集合）。每个索引有唯一的名字，通过这个名字来操作它。一个集群中可以有任意多个索引。

- **Type 类型**：指在一个索引中，可以索引不同类型的文档，如用户数据、博客数据。从6.0.0 版本起已废弃，一个索引中只存放一类数据。
 
- **Document 文档**：被索引的一条数据，索引的基本信息单元，以JSON格式来表示。 

- **Shard 分片**：在创建一个索引时可以指定分成多少个分片来存储。每个分片本身也是一个功能完善且独立的“索引”，可以被放置在集群的任意节点上。 

- **Replication 备份**: 一个分片可以有多个备份（副本）

与 MySQL 的对比：

| **MySQL** | **Elasticsearch** | **说明**                                                     |
| --------- | ----------------- | ------------------------------------------------------------ |
| Table     | Index             | 索引(index)，就是文档的集合，类似数据库的表(table)           |
| Row       | Document          | 文档（Document），就是一条条的数据，类似数据库中的行（Row），文档都是JSON格式 |
| Column    | Field             | 字段（Field），就是JSON文档中的字段，类似数据库中的列（Column） |
| Schema    | Mapping           | Mapping（映射）是索引中文档的约束，例如字段类型约束。类似数据库的表结构（Schema） |
| SQL       | DSL               | DSL是elasticsearch提供的JSON风格的请求语句，用来操作elasticsearch，实现CRUD |



## 2. 单点部署 es

### 2.1 创建网络

因为还需要部署 kibana 容器，因此需要让 es 和 kibana 容器互联——[docker 容器互联](https://vuepress.mirror.docker-practice.com/network/linking/#)。这里先创建一个网络：

```shell
docker network create es-net
```

### 2.2 创建镜像

拉取 es 镜像，这里用的是 7.12.1

```shell
docker pull elasticsearch:7.12.1
```

es 用的是 7.12.1 ，kibana 也应要用这个版本与其相对应

```shell
docker pull kibana:7.12.1
```

### 2.3 创建并运行 es 容器

```shell
docker run -d \
	--name es \
    -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
    -e "discovery.type=single-node" \
    -v es-data:/usr/share/elasticsearch/data \
    -v es-plugins:/usr/share/elasticsearch/plugins \
    --privileged \
    --network es-net \
    -p 9200:9200 \
    -p 9300:9300 \
elasticsearch:7.12.1
```
相关的命令解析:

- `-e "cluster.name=es-docker-cluster"`：设置集群名称
- `-e "http.host=0.0.0.0"`：监听的地址，可以外网访问
- `-e "ES_JAVA_OPTS=-Xms512m -Xmx512m"`：内存大小，考虑到服务器内存可能没这么大不然部署不起来
- `-e "discovery.type=single-node"`：非集群模式
- `-v es-data:/usr/share/elasticsearch/data`：挂载逻辑卷，绑定es的数据目录
- `-v es-logs:/usr/share/elasticsearch/logs`：挂载逻辑卷，绑定es的日志目录
- `-v es-plugins:/usr/share/elasticsearch/plugins`：挂载逻辑卷，绑定es的插件目录
- `--privileged`：授予逻辑卷访问权
- `--network es-net` ：加入一个名为es-net的网络中
- `-p 9200:9200`：端口映射配置

在浏览器中输入 `ip:9200` 即可看到 `elasticsearch` 的响应结果：

![](https://resource.lzyan.fun/PigGo/20220419132802.png)

### 2.4 创建并运行 kibana 容器

Kibana 是界面化的查询数据的工具，供一个 elasticsearch 的可视化界面

```shell
docker run -d \
--name kibana \
-e ELASTICSEARCH_HOSTS=http://es:9200 \
--network=es-net \
-p 5601:5601  \
kibana:7.12.1
```
相关命令解析：

- `--network es-net` ：加入一个名为es-net的网络中，与 elasticsearch 在同一个网络中
- `-e ELASTICSEARCH_HOSTS=http://es:9200"`：设置 elasticsearch 的地址，因为kibana已经与elasticsearch在一个网络，因此可以用容器名直接访问 elasticsearch
- `-p 5601:5601`：端口映射配置

kibana启动一般比较慢，需要多等待一会，可以通过命令查看启动的日志：

```shell
docker logs -f kibana
```

当启动成功后在浏览器输入：`ip:5601`，即可看到结果，其中 DevTools 工具可以编写 DSL 来操作 elasticsearch。

![](https://resource.lzyan.fun/PigGo/20220419133826.png)


## 3. 安装 IK 分词器

Ik分词器就是一款中文分词器。分词：把一段文字划分成一个个关键字，我们在搜索的时候会对自己的信息进行分词，会把数据库中或者索引库中的数据进行分词，然后进行一个匹配操作。

默认的中文分词会将每个字看成一个词，比如“我是天才“会被分成 我、是、天、才，这显然是不符合要求的，可能我们需要分成 我、是、天才、我是天才，所以我们需要安装Ik分词器来解决这个问题。

如果要对中文进行分词，建议使用Ik分词器。K分词器提供了两个分词算法。

- ik_smart ：最少切分

- ik_max_word：最细粒度切分

### 3.1 在线安装

```shell
# 进入容器内部
docker exec -it elasticsearch /bin/bash

# 在线下载并安装
./bin/elasticsearch-plugin  install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.12.1/elasticsearch-analysis-ik-7.12.1.zip

#退出
exit
#重启容器
docker restart elasticsearch
```

### 3.2 离线安装

首先上去他的 [代码仓库](https://github.com/medcl/elasticsearch-analysis-ik/releases) 把对应的版本下载下来

![](https://resource.lzyan.fun/PigGo/20220419135144.png)

安装插件需要知道 elasticsearch 的 plugins 目录位置，而我们用了数据卷挂载，因此需要查看 elasticsearch 的数据卷目录，通过下面命令查看可知 plugins 目录被挂载到了：`/var/lib/docker/volumes/es-plugins/_data `这个目录中。

```shell
[root@localhost lzyan]# docker volume inspect es-plugins
[
    {
        "CreatedAt": "2022-04-17T13:05:09+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/es-plugins/_data",
        "Name": "es-plugins",
        "Options": null,
        "Scope": "local"
    }
]
```
在 `_data` 目录下创建一个名了 `ik` 的文件夹，把下载下来的文件拷进去

![](https://resource.lzyan.fun/PigGo/20220419135605.png)

重启容器

```shell
docker restart es
```

### 3.3 测试

在 `kibana` 的 DevTools 工具中进行查询测试

```DSL
GET /_analyze
{
  "analyzer": "ik_max_word", # 最细粒度切分
  "text": "http常见面试题"  # 进行分词的内容
}
```
返回结果
```
{
  "tokens" : [
    {
      "token" : "http",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "ENGLISH",
      "position" : 0
    },
    {
      "token" : "常见",
      "start_offset" : 4,
      "end_offset" : 6,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "见面",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "面试题",
      "start_offset" : 6,
      "end_offset" : 9,
      "type" : "CN_WORD",
      "position" : 3
    },
    {
      "token" : "面试",
      "start_offset" : 6,
      "end_offset" : 8,
      "type" : "CN_WORD",
      "position" : 4
    },
    {
      "token" : "试题",
      "start_offset" : 7,
      "end_offset" : 9,
      "type" : "CN_WORD",
      "position" : 5
    }
  ]
}
```

### 3.4 自定义词典

随着互联网的发展，各种新梗新词层出不穷，在原有的词汇列表中并不存在。所以词汇需要不断更新才能更好地进行分词操作，IK分词器提供了扩展词汇的功能。

在 `ik/config` 目录下有个文件叫 `IKAnalyzer.cfg.xml` 的配置文件以及一些 dic 词典文件

![](https://resource.lzyan.fun/PigGo/20220419141215.png)

`IKAnalyzer.cfg.xml` 中文件的内容

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<comment>IK Analyzer 扩展配置</comment>
	<!--用户可以在这里配置自己的扩展字典 -->
	<entry key="ext_dict"></entry>
	 <!--用户可以在这里配置自己的扩展停止词字典-->
	<entry key="ext_stopwords"></entry>
	<!--用户可以在这里配置远程扩展字典 -->
	<!-- <entry key="remote_ext_dict">words_location</entry> -->
	<!--用户可以在这里配置远程扩展停止词字典-->
	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>

```

当想要添加新词语，在 `IKAnalyzer.cfg.xml` 配置文件内容添加：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
        <comment>IK Analyzer 扩展配置</comment>
        <!--用户可以在这里配置自己的扩展字典 *** 添加扩展词典-->
        <entry key="ext_dict">ext.dic</entry>
</properties>
```

然后在 `config` 文件夹中创建一个 `ext.dic` 文件，添加新词

```
白嫖
永远滴神
```

## 4 集群部署 es

单机的 `elasticsearch` 做数据存储，必然面临两个问题：海量数据存储问题、单点故障问题。集群的解决方案：

- 海量数据存储问题：将索引库从逻辑上拆分为 N 个分片（shard），存储到多个节点
- 单点故障问题：将分片数据在不同节点备份（replica ）

这里使用的是单机上利用 `docker` 容器运行多个 `es` 实例来模拟集群。直接使用 `docker-compose` 工具来完成，但这要求你的 `Linux` 虚拟机至少有 `4G` 的内存空间。

### 4.1 创建 es 集群

编写 `docker-compose.yml` 文件如下：

```yml
version: '2.2'
services:
  es01:
    image: elasticsearch:7.12.1
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03 //这里其实是另外两个容器的ip地址，但这里是基于docker单机演示集群，所以docker容器之间可相互访问填容器名即可
      - cluster.initial_master_nodes=es01,es02,es03 // 主从节点 选举
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: elasticsearch:7.12.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data02:/usr/share/elasticsearch/data
    ports:
      - 9201:9200
    networks:
      - elastic
  es03:
    image: elasticsearch:7.12.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic
    ports:
      - 9202:9200
volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

启动前建议先清下缓存:

```sh
sudo echo 3 > /proc/sys/vm/drop_caches
```

还有 es 运行需要修改一些 linux 系统权限，修改 `/etc/sysctl.conf` 文件

```sh
vi /etc/sysctl.conf
```

添加下面的内容（放开虚拟机内存大小）：

```sh
vm.max_map_count=262144
```

然后执行命令，让配置生效：

```sh
sysctl -p
``` 

通过 `docker-compose` 创建和启动集群，`-d` 守护式进程启动：

```sh
docker-compose up -d
```

### 4.2 集群状态监控

`kibana` 可以监控 es 集群，不过新版本需要依赖 es 的x-pack 功能，配置比较复杂。

这里推荐使用 `cerebro` 来监控 es 集群状态。Cerebro 是 Elasticsearch 版本 5.x 以前插件 Elasticsearch Kopf 的演变，可以通过图形界面查看分片分配和执行常见的索引操作，比 Kiban a轻量很多。[官方下载](https://github.com/lmenezes/cerebro)，下载解压后进入 bin 目录，双击打开 cerebro.bat 文件即可启动。

随后访问管理界面：http://localhost:9200

![](https://resource.lzyan.fun/PigGo/20220422145735.png)

输入 elasticsearch 的任意节点的地址和端口，点击 connect ：绿色的条，代表集群处于绿色（健康状态）。

![](https://resource.lzyan.fun/PigGo/20220422145753.png)

### 4.3 ES集群的节点角色

elasticsearch 中集群节点有不同的职责划分：

|    节点类型     |                  配置参数                  | 默认值 |                           节点职责                           |
| :-------------: | :----------------------------------------: | :----: | :----------------------------------------------------------: |
| master eligible |                node.master                 |  true  | 备选主节点：主节点可以管理和记录集群状态、决定分片在哪个节点、处理创建和删除索引库的请求 |
|      data       |                 node.data                  |  true  |             数据节点：存储数据、搜索、聚合、CRUD             |
|     ingest      |                node.ingest                 |  true  |                     数据存储之前的预处理                     |
|  coordinating   | 上面3个参数都为false时则为coordinating节点 |   无   |     路由请求到其他节点、合并其他节点处理的结果返回给用户     |

### 4.4 用 cerebor 创建索引库

填写信息并创建索引库：

![](https://resource.lzyan.fun/PigGo/20220422145905.png)

![](https://resource.lzyan.fun/PigGo/20220422150103.png)

回到首页即可查看索引库分片的情况:

![](https://resource.lzyan.fun/PigGo/20220422150445.png)

或使用语法，填写在 setting 处：

```json
PUT /lzyan_blog
{
  "settings": {
    "number_of_shards": 3, // 分片数量
    "number_of_replicas": 1 // 副本数量
  },
  "mappings": {
    "properties": {
      // mapping映射定义 ...
    }
  }
}
```

### 4.5 ES 集群的故障转移

集群的 master 节点会监控集群中的节点状态，如果发现有节点宕机，会立即将宕机节点的分片数据迁移到其它节点，确保数据安全，这个叫做故障转移。

假设我现在直接把 es03 这台停掉，此时集群状态监控:

![](https://resource.lzyan.fun/PigGo/20220422151132.png)

再过多一会，就会发现 es03 这台的分片数据就会迁移到了其他的节点，且重新选举了 es02 这台为 master 节点：

![](https://resource.lzyan.fun/PigGo/20220422151224.png)

然后现在重新启动 es03 ，分片数据重新选配迁移回来了，es02 依然是 master 节点：

![](https://resource.lzyan.fun/PigGo/20220422151724.png)

## 5. ElasticSearch 知识体系

[ElasticSearch知识体系详解](https://www.pdai.tech/md/db/nosql-es/elasticsearch.html)
