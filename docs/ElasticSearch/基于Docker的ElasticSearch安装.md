## 1. 什么是 ElasticSearch 

`ElasticSearch` 是一款非常强大的、基于 `Lucene` 的开源搜索及分析引擎；它是一个实时的分布式搜索分析引擎，它能让你以前所未有的速度和规模，去探索你的数据。

它被用作全文检索、结构化搜索、分析以及这三个功能的组合：

- Wikipedia 使用 Elasticsearch 提供带有高亮片段的全文搜索，还有 search-as-you-type 和 did-you-mean 的建议。 
- 卫报 使用 Elasticsearch 将网络社交数据结合到访客日志中，为它的编辑们提供公众对于新文章的实时反馈。
- Stack Overflow 将地理位置查询融入全文检索中去，并且使用 more-like-this 接口去查找相关的问题和回答。 
- GitHub 使用 Elasticsearch 对1300亿行代码进行查询。 
- ...

除了搜索，结合 **Kibana、Logstash、Beats** 开源产品，**Elastic Stack（简称ELK）** 还被广泛运用在大数据近实时分析领域，包括：**日志分析、指标监控、信息安全**等。它可以帮助你探索 **海量结构化、非结构化数据，按需创建可视化报表，对监控数据设置报警阈值，通过使用机器学习，自动识别异常状况。**

`ElasticSearch` 是基于 `Restful WebApi`，使用Java语言开发的搜索引擎库类，并作为Apache 许可条款下的开放源码发布，是当前流行的企业级搜索引擎。其客户端在Java、C#、PHP、Python等许多语言中都是可用的。

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

## 4. ElasticSearch 知识体系

[ElasticSearch知识体系详解](https://www.pdai.tech/md/db/nosql-es/elasticsearch.html)
