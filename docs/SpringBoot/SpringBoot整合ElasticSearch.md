## 前言

本文整合，安装 `ElasticSearch + Kibana`，以及于 `Springboot` 的基本使用方式。

安装环境是 阿里云ECS  `CentOS Linux release 7.2.1511 (Core) 一核两G` 。

由于服务器资源有限，所以选择的是原生安装，没有使用 docker 来快速安装。

## 介绍

### ElasticSearch

- ElasticSearch 是一款非常强大的、基于 Lucene 的开源搜索及分析引擎；它是一个实时的分布式搜索分析引擎，它能让你以前所未有的速度和规模，去探索你的数据。

它被用作全文检索、结构化搜索、分析以及这三个功能的组合：

- Wikipedia 使用 Elasticsearch 提供带有高亮片段的全文搜索，还有 search-as-you-type 和 did-you-mean 的建议。 

- 卫报 使用 Elasticsearch 将网络社交数据结合到访客日志中，为它的编辑们提供公众对于新文章的实时反馈。 
- Stack Overflow 将地理位置查询融入全文检索中去，并且使用 more-like-this 接口去查找相关的问题和回答。 

- GitHub 使用 Elasticsearch 对1300亿行代码进行查询。

除了搜索，结合Kibana、Logstash、Beats开源产品，Elastic Stack（简称ELK），ELK 技术栈还被广泛运用在大数据近实时分析领域，包括：日志分析、指标监控、信息安全等。它可以帮助你探索海量结构化、非结构化数据，按需创建可视化报表，对监控数据设置报警阈值，通过使用机器学习，自动识别异常状况。

ElasticSearch 是基于 Restful WebApi ，使用 Java 语言开发的搜索引擎库类，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。其客户端在 Java、C#、PHP、Python 等许多语言中都是可用的。

### Kibana

Kibana是一个开源的分析与可视化平台，设计出来用于和Elasticsearch一起使用的。你可以用kibana搜索、查看存放在Elasticsearch中的数据。Kibana与Elasticsearch的交互方式是各种不同的图表、表格、地图等，直观的展示数据，从而达到高级的数据分析与可视化的目的。

## 安装 

### ElasticSearch

> ElasticSearch 是基于Java平台的，所以先要安装较新的 jdk (下载连接)[https://www.oracle.com/java/technologies/downloads/#java8]，安装过程：略

在当前文件夹（要下载在哪自己确定）下载 ElasticSearch

```sh
curl -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.12.0-linux-x86_64.tar.gz
```

解压 
```sh
tar zxvf elasticsearch-7.12.0-linux-x86_64.tar.gz 
```

增加一个非 root 用户，因为 ElasticSearch5 及以上版本，基于安全考虑，强制规定不能以 root 身份运行，所以必须创建一个非 root 用户来运行 ElasticSearch。如果你使用 root 用户来启动 ElasticSearch ，则会有如下错误信息：

```java
org.elasticsearch.bootstrap.StartupException: java.lang.RuntimeException: can not run elasticsearch as root
	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:163) ~[elasticsearch-7.12.0.jar:7.12.0]
	at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:150) ~[elasticsearch-7.12.0.jar:7.12.0]
	at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:75) ~[elasticsearch-7.12.0.jar:7.12.0]
	at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:116) ~[elasticsearch-cli-7.12.0.jar:7.12.0]
	at org.elasticsearch.cli.Command.main(Command.java:79) ~[elasticsearch-cli-7.12.0.jar:7.12.0]
	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:115) ~[elasticsearch-7.12.0.jar:7.12.0]
	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:81) ~[elasticsearch-7.12.0.jar:7.12.0]
Caused by: java.lang.RuntimeException: can not run elasticsearch as root
	at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:101) ~[elasticsearch-7.12.0.jar:7.12.0]
	at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:168) ~[elasticsearch-7.12.0.jar:7.12.0]
	at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:397) ~[elasticsearch-7.12.0.jar:7.12.0]
	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:159) ~[elasticsearch-7.12.0.jar:7.12.0]
	... 6 more
uncaught exception in thread [main]
java.lang.RuntimeException: can not run elasticsearch as root
	at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:101)
	at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:168)
	at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:397)
	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:159)
	at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:150)
	at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:75)
	at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:116)
	at org.elasticsearch.cli.Command.main(Command.java:79)
	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:115)
	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:81)
For complete error details, refer to the log at /root/ElasticSearch/elasticsearch-7.12.0/logs/elasticsearch.log
```

增加一个非 root 用户 elasticsearch 来运行，密码：zwt123456.

```sh
# 增加elasticsearch用户
New password: 
BAD PASSWORD: The password contains the user name in some form
Retype new password: 
passwd: all authentication tokens updated successfully.

# 修改目录权限至新增的elasticsearch用户
[root@VM-0-14-centos elasticsearch-7.12.0]# chown -R elasticsearch /elasticsearch-7.12.0
# 增加data和log存放区，并赋予elasticsearch用户权限
[root@VM-0-14-centos elasticsearch-7.12.0]# mkdir -p /data/es
[root@VM-0-14-centos elasticsearch-7.12.0]# chown -R elasticsearch /data/es
[root@VM-0-14-centos elasticsearch-7.12.0]# mkdir -p /var/log/es
[root@VM-0-14-centos elasticsearch-7.12.0]# chown -R elasticsearch /var/log/es
```

然后修改上述的data和log路径，`vi /opt/elasticsearch-7.12.0/config/elasticsearch.yml`

```sh
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
path.data: /data/es
#
# Path to log files:
#
path.logs: /var/log/es
```

修改Linux系统的限制配置。

1. 修改系统中允许应用最多创建多少文件等的限制权限。Linux默认来说，一般限制应用最多创建的文件是65535个。但是ES至少需要65536的文件创建权限
2. 修改系统中允许用户启动的进程开启多少个线程。默认的Linux限制root用户开启的进程可以开启任意数量的线程，其他用户开启的进程可以开启1024个线程。必须修改限制数为4096+。因为ES至少需要4096的线程池预备。ES在5.x版本之后，强制要求在linux中不能使用root用户启动ES进程。所以必须使用其他用户启动ES进程才可以。
3. Linux低版本内核为线程分配的内存是128K。4.x版本的内核分配的内存更大。如果虚拟机的内存是1G，最多只能开启3000+个线程数。至少为虚拟机分配1.5G以上的内存。

修改如下配置，`vi /etc/security/limits.conf`
```sh
elasticsearch soft nofile 65536
elasticsearch hard nofile 65536
elasticsearch soft nproc 4096
elasticsearch hard nproc 4096
```

切换到 elasticsearch 用户，然后启动 ElasticSearch（注意前面安装时 jdk、elasticsearch 都不要放在root文件夹，否则会有问题，建议放在opt下）

```sh
su elasticsearch
./bin/elasticsearch -d
```

查看是否安装成功

```sh
## 查看端口是否启动
netstat -ntlp | grep 9200

## curl 127.0.0.1:9200
{
  "name" : "izwz95oq74pfx71dkkc1sxz",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "36uDv3rqTp2QgEaX2U9Wjg",
  "version" : {
    "number" : "7.12.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "78722783c38caa25a70982b5b042074cde5d3b3a",
    "build_date" : "2021-03-18T06:17:15.410153305Z",
    "build_snapshot" : false,
    "lucene_version" : "8.8.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

```

### Kibana

官网下载 [戳](https://www.elastic.co/cn/downloads/kibana)

传到服务器上，解压

```sh
tar zxvf kibana-8.1.0-linux-x86_64.tar.gz
```

给 elasticsearch 用户权限

```sh

chown -R elasticsearch /opt/kibana/kibana-8.1.0

```

配置Kibana的远程访问

```sh
vi /opt/kibana/kibana-8.1.0/config/kibana.yml

server.host: 0.0.0.0
```

