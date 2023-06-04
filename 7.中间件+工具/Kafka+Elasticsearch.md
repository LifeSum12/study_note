# Kafka

https://www.w3cschool.cn/apache_kafka/

启动zookeeper

```cmd
bin\windows\zookeeper-server-start.bat config\zookeeper.properties
```

启动kafka

```cmd
bin\windows\kafka-server-start.bat config\server.properties
```

## 概述

消息系统负责将数据从一个应用程序传输到另一个应用程序，因此应用程序可以专注于数据，但不担心如何共享它。

### 两种类型

一种是点对点。

一种是发布 - 订阅(pub-sub)消息系统。 大多数消息模式遵循 **pub-sub** 。

### Kafka好处

- **可靠性** - Kafka是分布式，分区，复制和容错的。

- **可扩展性** - Kafka消息传递系统轻松缩放，无需停机。

- **耐用性** - Kafka使用分布式提交日志，这意味着消息会尽可能快地保留在磁盘上，因此它是持久的。

- **性能** - Kafka对于发布和订阅消息都具有高吞吐量。 即使存储了许多TB的消息，它也保持稳定的性能。

## 基础

对于海量数据如何存储？分布式文件系统。

要构建分布式应用还需要一些工具，比如分布式协调服务 Zookeeper 等等。

Kafka 专为分布式高吞吐量系统而设计。

**简介 ：Apache Kafka 是一个分布式发布 - 订阅消息系统和一个强大的队列，可以处理大量的数据，并使你能够将消息从一个端点传递到另一个端点。** 

 Kafka 消息保留在磁盘上，并在群集内复制以防止数据丢失。

 Kafka 构建在 ZooKeeper 同步服务之上。

### 关键术语

**生产者和消费者（producer和consumer）**

**主题（topic）**

**broker：**Kafka 集群中有很多台 Server，其中每一台 Server 都可以存储消息，将每一台 Server 称为一个 kafka 实例，也叫做 broker。

**分区（partition）：**每个 topic 都可以分成多个 partition，每个 partition 在存储层面是 一个文件。消息写入就是简单的文件追加。为什么要分区，因为面向大数据。另外，每个 partition 可以有一定的副本，备份到多台机器上。

**偏移量（Offset）：**一个分区对应一个磁盘上的文件，而消息在文件中的位置就称为 offset（偏移量），offset 为一个 long 型数字，它可以唯一标记一条消息。。由于kafka 并没有提供其他额外的索引机制来存储 offset，文件只能顺序的读写，所以在kafka中几乎不允许对消息进行“随机读写”。



- 即使消息被消费，kafka 也不会立即删除该消息，可以通过配置使得过一段时间后自动删除以释放磁盘空间

### 基本原理

#### 分布式和分区（distributed、partitioned）

总结：一个 topic 对应的多个 partition 分散存储到集群中的多个 broker 上，存储方式是一个 partition 对应一个文件，每个 broker 负责存储在自己机器上的 partition 中的消息读写。

#### 副本（replicated ）

 既然有副本，就涉及到对同一个文件的多个备份如何进行管理和调度。kafka 采取的方案是：每个 partition 选举一个 server 作为“leader”，由 **leader 负责所有对该分区的读写**，**其他 server 作为 follower 只需要简单的与 leader 同步**，保持跟进即可。如果原来的 leader 失效，会重新选举由其他的 follower 来成为新的 leader。

​	至于如何**选取 leader**， ZooKeeper擅长。

​	作为 leader 的 server 承担了该分区所有的读写请求，因此其压力是比较大的，从整体考虑，有多少个 partition 就意味着会有多少个leader，kafka 会将 leader 分散到不同的 broker 上，确保整体的**负载均衡**。

#### 整体数据流程

**Producer** 

​		对于生产者要写入的一条记录，可以指定四个参数：分别是 topic、partition、key 和 value，其中 topic 和 value（要写入的数据）是必须要指定的，而 key 和 partition 是可选的。对于一条记录，先对其进行序列化，然后按照 Topic 和 Partition，放进对应的发送队列中。

​	 producer 将会和Topic下所有 partition leader 保持 socket 连接，消息由 producer 直接通过 socket 发送到 broker。其中 partition leader 的位置( host : port )注册在 zookeeper 中，producer 作为 zookeeper client，已经注册了 watch 用来监听 partition leader 的变更事件，因此，可以准确的知道谁是当前的 leader。

​		producer 端采用异步发送：将多条消息暂且在客户端 buffer 起来，并将他们批量的发送到 broker，小数据 IO 太多，会拖慢整体的网络延迟，批量延迟发送事实上提升了网络效率。

**Consume**

​		对于消费者，不是以单独的形式存在的，每一个消费者属于一个 consumer group，一个 group 包含多个 consumer。特别需要注意的是：订阅 Topic 是以一个消费组来订阅的，发送到 Topic 的消息，只会被订阅此 Topic 的每个 group 中的一个 consumer 消费。

  如果所有的 Consumer 都具有相同的 group，那么就像是一个点对点的消息系统；如果每个 consumer 都具有不同的 group，那么消息会广播给所有的消费者。

  具体说来，这实际上是根据 partition 来分的，一个 Partition，只能被消费组里的一个消费者消费，但是可以同时被多个消费组消费，消费组里的每个消费者是关联到一个 partition 的，因此有这样的说法：对于一个 topic,同一个 group 中不能有多于 partitions 个数的 consumer 同时消费,否则将意味着某些 consumer 将无法得到消息。

  同一个消费组的两个消费者不会同时消费一个 partition。

![img](https://atts.w3cschool.cn/attachments/day_200916/202009161733117671.png)

#### 消息传送机制

Kafka 支持 3 种消息投递语义,在业务中，常常都是使用 At least once 的模型。

- At most once：最多一次，消息可能会丢失，但不会重复。
- At least once：最少一次，消息不会丢失，可能会重复。
- Exactly once：只且一次，消息不丢失不重复，只且消费一次。



## Kafka 集群架构

![Cluster Architecture](https://atts.w3cschool.cn/attachments/tuploads/apache_kafka/cluster_architecture.jpg)



**Broker（代理）**

Kafka集群通常由多个代理组成以保持负载平衡。 Kafka代理是无状态的，所以他们使用ZooKeeper来维护它们的集群状态。 一个Kafka代理实例可以每秒处理数十万次读取和写入，每个Broker可以处理TB的消息，而没有性能影响。 Kafka经纪人领导选举可以由ZooKeeper完成。

**ZooKeeper**

ZooKeeper用于管理和协调Kafka代理。 ZooKeeper服务主要用于通知生产者和消费者Kafka系统中存在任何新代理或Kafka系统中代理失败。 根据Zookeeper接收到关于代理的存在或失败的通知，然后生产者和消费者采取决定并开始与某些其他代理协调他们的任务。

**Producers**

生产者将数据推送给经纪人。 当新代理启动时，所有生产者搜索它并自动向该新代理发送消息。 Kafka生产者不等待来自代理的确认，并且发送消息的速度与代理可以处理的一样快。

**Consumers**

因为Kafka代理是无状态的，这意味着消费者必须通过使用分区偏移来维护已经消耗了多少消息。 如果消费者确认特定的消息偏移，则意味着消费者已经消费了所有先前的消息。 消费者向代理发出异步拉取请求，以具有准备好消耗的字节缓冲区。 消费者可以简单地通过提供偏移值来快退或跳到分区中的任何点。 消费者偏移值由ZooKeeper通知。



## Kafka 工作流程

Kafka 只是分为一个或多个分区的主题的集合。Kafka 分区是消息的线性有序序列，其中每个消息由它们的索引(称为偏移)来标识。Kafka 集群中的所有数据都是不相连的分区联合。 传入消息写在分区的末尾，消息由消费者顺序读取。 通过将消息复制到不同的代理提供持久性。

Kafka 以快速，可靠，持久，容错和零停机的方式提供基于pub-sub 和队列的消息系统。 在这两种情况下，生产者只需将消息发送到主题，消费者可以根据自己的需要选择任何一种类型的消息传递系统。 让我们按照下一节中的步骤来了解消费者如何选择他们选择的消息系统。

### 发布 - 订阅消息的工作流程

以下是 Pub-Sub 消息的逐步工作流程 -

- 生产者定期向主题发送消息。
- Kafka 代理存储为该特定主题配置的分区中的所有消息。 它确保消息在分区之间平等共享。 如果生产者发送两个消息并且有两个分区，Kafka 将在第一分区中存储一个消息，在第二分区中存储第二消息。
- 消费者订阅特定主题。
- 一旦消费者订阅主题，Kafka 将向消费者提供主题的当前偏移，并且还将偏移保存在 Zookeeper 系统中。
- 消费者将定期请求 Kafka (如100 Ms)新消息。
- 一旦 Kafka 收到来自生产者的消息，它将这些消息转发给消费者。
- 消费者将收到消息并进行处理。
- 一旦消息被处理，消费者将向 Kafka 代理发送确认。
- 一旦 Kafka 收到确认，它将偏移更改为新值，并在 Zookeeper 中更新它。 由于偏移在 Zookeeper 中维护，消费者可以正确地读取下一封邮件，即使在服务器暴力期间。
- 以上流程将重复，直到消费者停止请求。
- 消费者可以随时回退/跳到所需的主题偏移量，并阅读所有后续消息。

### 队列消息/用户组的工作流

在队列消息传递系统而不是单个消费者中，具有相同组 ID 的一组消费者将订阅主题。 简单来说，订阅具有相同 Group ID 的主题的消费者被认为是单个组，并且消息在它们之间共享。 让我们检查这个系统的实际工作流程。

- 生产者以固定间隔向某个主题发送消息。
- Kafka存储在为该特定主题配置的分区中的所有消息，类似于前面的方案。
- 单个消费者订阅特定主题，假设 Topic-01 为 Group ID 为 Group-1 。
- Kafka 以与发布 - 订阅消息相同的方式与消费者交互，直到新消费者以相同的组 ID 订阅相同主题Topic-01 1 。
- 一旦新消费者到达，Kafka 将其操作切换到共享模式，并在两个消费者之间共享数据。 此共享将继续，直到用户数达到为该特定主题配置的分区数。
- 一旦消费者的数量超过分区的数量，新消费者将不会接收任何进一步的消息，直到现有消费者取消订阅任何一个消费者。 出现这种情况是因为 Kafka 中的每个消费者将被分配至少一个分区，并且一旦所有分区被分配给现有消费者，新消费者将必须等待。
- 此功能也称为使用者组。 同样，Kafka 将以非常简单和高效的方式提供两个系统中最好的。

### ZooKeeper 的作用

Apache Kafka 的一个关键依赖是 Apache Zookeeper，它是一个分布式配置和同步服务。Zookeeper 是 Kafka 代理和消费者之间的协调接口。Kafka 服务器通过 Zookeeper 集群共享信息。Kafka 在 Zookeeper 中存储基本元数据，例如关于主题，代理，消费者偏移(队列读取器)等的信息。

由于所有关键信息存储在 Zookeeper 中，并且它通常在其整体上复制此数据，因此Kafka代理/ Zookeeper 的故障不会影响 Kafka 集群的状态。Kafka 将恢复状态，一旦 Zookeeper 重新启动。 这为Kafka带来了零停机时间。Kafka 代理之间的领导者选举也通过使用 Zookeeper 在领导者失败的情况下完成。





# Elasticsearch

**为什么需要？**

mysql当然也能实现数据的搜索和分析 例如求年龄的平均值 avg 分组group by 等等 但是我们说术业有专攻，百万数据时，Elasticsearch更快。

**底层**

Elastisearch的底层是开元库Lucene。但是你没法直接调用lucene,必须自己写代码去调用他的接口。Elasticsearch是lucene的封装，提供了REST API的操作接口 开箱即用。

## 基本概念

**1、Index（索引）**

动词，相当于mysql中的insert

名词，相当于mysql的Database

*2、Type（类型）7.0废弃*

在index（索引）中，可以定义一个或多个类型，类似于mysql中的table，同一种类型的数据放在一起

**3、 Document（文档）**

保存在某个索引（Index）下、某种类型（Type）的一个数据（Document），文档是json格式的数据，Document1相当于mysql中某个table数据

![elasticsearch概念](https://segmentfault.com/img/remote/1460000038895417)

**4. Field(文档字段)**

文档由多个json字段（Field）组成， 这里的字段类似mysql中表的字段。

当然Elasticsearch中字段也有类型的，下面是常用的字段类型:

- 数值类型（包括: long、integer、short、byte、double、float）
- text - 支持全文搜索
- keyword - 不支持全文搜索，例如：email、电话这些数据，作为一个整体进行匹配就可以，不需要分词处理。
- date - 日期类型
- boolean

**5. mapping (映射)**

Elasticsearch的mapping (映射)类似mysql中的表结构定义，每个索引都有一个映射规则，我们可以通过定义索引的映射规则，提前定义好文档的json结构和字段类型，如果没有定义索引的映射规则，Elasticsearch会在写入数据的时候，根据我们写入的数据字段推测出对应的字段类型，相当于自动定义索引的映射规则。

##  类比MYSQL存储结构

| Elasticsearch存储结构 | MYSQL存储结构 |
| --------------------- | ------------- |
| index(索引)           | 数据库名称    |
| Document (文档)       | 表            |
| Field(字段）          | 表字段        |
| mapping (映射)        | 表结构定义    |

**shards (分片)：**

​	把一个索引的数据，分配到不同节点上。可提供的性能和吞吐量。

**replicas(副本):**

 	把一个索引的数据，在其他结点建立副本，避免单点故障数据丢失。

## 操作

使用 Elasticsearch 官方提供的 RESTful API 进行交互，通过 HTTP 请求来访问 Elasticsearch 数据库，执行增PUT、删DELETE、改POST、查GET等操作。

1.使用curl命令发送http请求

```cmd
curl -X GET "localhost:9200/_cat/health?v&pretty"
```

**2.postman可视化工具发送http请求**

3.Kibana可视化操作

![img](https://www.tizi365.com/wp-content/uploads/2020/01/kibana-console-1-1024x554.jpg)



## Postman操作

![image-20230426193853712](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202304261938794.png)

**插入数据：**

​	**URL 指定插入的服务器、索引 + 文档 （通俗来讲数据库和表）。**

​	**Body请求体指定插入的值。**

- ES 7.0 Type弃用，默认_doc作占位符。 

- 请求体默认使用Json格式。

ES的反馈就是发HTTP响应，响应体包含各种信息。



Elasticsearch默认开启9200和9300端口。

9200 使用Http访问

9300 使用TCP访问（应用一般用这个）



## Spring整合ES

导入相关包，配置文件配置。

```yml
  data:
    elasticsearch:
      cluster-name: my-application
      cluster-nodes: localhost:9300
```

与实体类建立联系（Mysql与ES与Java类）：

```java
@Document(indexName = "discusspost", type = "_doc", shards = 6, replicas = 3)
@Data
public class DiscussPost {
    @Id
    private int id;
    @Field(type = FieldType.Integer)
    private int userId;
    //存储分词器analyzer，尽可能增大搜索访问。
    //搜索分词器searchAnalyzer，粗糙一点。
    @Field(type = FieldType.Text, analyzer = "ik_max_word", searchAnalyzer = "ik_smart")
    private String title;
    @Field(type = FieldType.Text, analyzer = "ik_max_word", searchAnalyzer = "ik_smart")
    private String content;
    @Field(type = FieldType.Integer)
    private int type;
    @Field(type = FieldType.Integer)
    private int status;
    @Field(type = FieldType.Date)
    private Date createTime;
    @Field(type = FieldType.Integer)
    private int commentCount;
    @Field(type = FieldType.Double)
    private Double score;
}
```

**@Document** 

- indexName 索引名
- shards 该索引分片数。（把一个索引的数据，分配到不同节点上）
- replicas 该索引副本数。（把一个索引的数据，备份到不同节点上）

**@Id** 

**@Field**	普通字段，指定类型。

**@Field(type = FieldType.Text, analyzer = "ik_max_word", searchAnalyzer = "ik_smart")**

关键内容，主要提供被搜索的内容要加入特殊设置，存储分词器和搜索分词器。

如“互联网实习”，在存储分词器处理后，存储为“互联”、“互联网”、“联网”、"网"、"网实"、"联网实习"、"互联网实现"等，按分词器的字典文件来分词。

- analyzer = "ik_max_word" 表示存储分词器要使用最大化分词模式进行分词，将更多的信息存储到倒排索引中，从而提高搜索的准确性和召回率。

- searchAnalyzer = "ik_smart" 表示搜索分词器用智能化、更简化分词模式进行分词。不用ik_max_word，因为会搜索变得过于严格，不能很好地容错。





