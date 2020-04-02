# Kafka ElasticSeach

## 目录
* 目录
* kafka基础知识介绍
* 消息队列分类
* 常见协议
* kafka
* 常用技术
* 其他MQ对比
* 重要概念
* 消息处理机制
* 持久化原理
* 多维管控中使用的topic说明
* topic划分规范
* topic命名规范
* group id命名规范
* 连接管理平台topic
* 数据使能平台topic
* 应用使能平台
* 业务使能平台topic
* KafkaTools使用
* ftp地址
* 配置步骤
* 使用介绍
* ES基础知识介绍
* 学习网站
* 回忆时光
* 是什么
* 特点
* 核心概念
* 类比Mysql
* 核心类型（Core datatype）
* 集群存储结构
* 创建索引
* 检查集群健康状态
* 多维管控中使用的索引介绍，ES head使用* 介绍
* ElasticSearch-head插件安装与使用说明
* 1）chrome head插件
2）Head服务器部署（nodejs服务）
* 3）head的使用
引用：

kafka基础知识介绍

消息队列分类

点对点


消息被消费后，将从queue中删除，已经被消费的消息不能再被消费
Queue支持多消费者，但是一个消息只能由一个消费者消费


订阅/发布


消息会被发送到一个topic中，多个消费者可以同时消费一条消息

常见协议

AMQP协议
AMQP即Advanced Message Queuing Protocol,一个提供统一消息服务的应用层标准高级消息队列协议,是应用层协议的一个开放标准,为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同开发语言等条件的限制。（RabbitMQ）
优点：可靠、通用
      MQTT协议
MQTT（Message Queuing Telemetry Transport，消息队列遥测传输）是IBM开发的一个即时通讯协议，有可能成为物联网的重要组成部分。该协议支持所有平台，几乎可以把所有联网物品和外部连接起来，被用来当做传感器和致动器（比如通过Twitter让房屋联网）的通信协议。 
优点：格式简洁、占用带宽小、移动端通信、PUSH、嵌入式系统
      STOMP协议
STOMP（Streaming Text Orientated Message Protocol）是流文本定向消息协议，是一种为MOM(Message Oriented Middleware，面向消息的中间件)设计的简单文本协议。STOMP提供一个可互操作的连接格式，允许客户端与任意STOMP消息代理（Broker）进行交互。 
优点：命令模式（非topic\queue模式）
      XMPP协议
XMPP（可扩展消息处理现场协议，Extensible Messaging and Presence Protocol）是基于可扩展标记语言（XML）的协议，多用于即时消息（IM）以及在线现场探测。适用于服务器之间的准即时操作。核心是基于XML流传输，这个协议可能最终允许因特网用户向因特网上的其他任何人发送即时消息，即使其操作系统和浏览器不同。 
优点：通用公开、兼容性强、可扩展、安全性高，但XML编码格式占用带宽大
      其他基于TCP/IP自定义的协议
有些特殊框架（如：redis、kafka、zeroMq等）根据自身需要未严格遵循MQ规范，而是基于TCP\IP自行封装了一套协议，通过网络socket接口进行传输，实现了MQ的功能。

kafka
kafka是一个分布式的发布-订阅消息系统。
由LinkedIn使用scala开发，后来被开源成为Apache项目的一部分。
分布式的、可划分的、多订阅者、冗余备份的持久性日志服务。
主要用于活跃的流式数据处理。
作用：
降低系统组网复杂度
降低编程复杂度（或者增加）
通过异步处理提高系统性能（削峰、减少响应所需时间）
降低系统耦合度
kafka 唯一的一点劣势是有可能消息重复消费


常用技术


开发语言
1）Kafka：Scala
2）RabbitMQ：Erlang
3）ZeroMQ：C语言
4）RocketMQ：Java
5）ActiveMQ：Java

支持的协议
1）Kafka：自己定义的一套…（基于TCP）
2）RabbitMQ：AMQP
3）ZeroMQ：TCP、UDP
4）RocketMQ：自己定义的一套…
5）ActiveMQ：OpenWire、STOMP、REST、XMPP、AMQP

消息存储
1）Kafka：
内存、磁盘、数据库。支持大量堆积。
Kafka的最小存储单元是分区，一个topic包含多个分区，Kafka创建主题时，这些分区会被分配在多个服务器上，通常一个broker一台服务器。
2）RabbitMQ：
内存、磁盘。支持少量堆积。
RabbitMQ的消息分为持久化的消息和非持久化消息，不管是持久化的消息还是非持久化的消息都可以写入到磁盘。
3）ZeroMQ：
消息发送端的内存或者磁盘中。不支持持久化。
4）RocketMQ：
磁盘。支持大量堆积。
5）ActiveMQ：
内存、磁盘、数据库。支持少量堆积。

消息事务
1）Kafka：支持
2）RabbitMQ：支持。客户端将信道设置为事务模式，只有当消息被RabbitMQ接收，事务才能提交成功，否则在捕获异常后进行回滚。使用事务会使得性能有所下降
3）ZeroMQ：不支持
4）RocketMQ：支持
5）ActiveMQ：支持

集群方式
1）Kafka：
天然的‘Leader-Slave’无状态集群，每台服务器既是Master也是Slave。
分区首领均匀地分布在不同的Kafka服务器上，分区副本也均匀地分布在不同的Kafka服务器上，所以每一台Kafka服务器既含有分区首领，同时又含有分区副本。每一台Kafka服务器是某一台Kafka服务器的Slave，同时也是某一台Kafka服务器的leader。Kafka的集群依赖于zookeeper，zookeeper支持热扩展，所有的broker、消费者、分区都可以动态加入移除，而无需关闭服务，与不依靠zookeeper集群的mq相比，这是最大的优势。
2）RabbitMQ：
支持简单集群，'复制'模式，对高级集群模式支持不好。
RabbitMQ的每一个节点，不管是单一节点系统或者是集群中的一部分，要么是内存节点，要么是磁盘节点，集群中至少要有一个是磁盘节点。
在RabbitMQ集群中创建队列，集群只会在单个节点创建队列进程和完整的队列信息（元数据、状态、内容），而不是在所有节点上创建。
引入镜像队列，可以避免单点故障，确保服务的可用性，但是需要人为地为某些重要的队列配置镜像。
3）ZeroMQ：
去中心化，不支持集群。
4）RocketMQ：
常用 多对'Master-Slave' 模式，开源版本需手动切换Slave变成Master。
Name Server是一个几乎无状态节点，可集群部署，节点之间无任何信息同步。
Broker部署相对复杂，Broker分为Master与Slave，一个Master可以对应多个Slave，但是一个Slave只能对应一个Master。
Master与Slave的对应关系通过指定相同的BrokerName，不同的BrokerId来定义，BrokerId为0表示Master，非0表示Slave。
Master也可以部署多个。每个Broker与Name Server集群中的所有节点建立长连接，定时注册Topic信息到所有Name Server。
Producer与Name Server集群中的其中一个节点（随机选择）建立长连接，定期从Name Server取Topic路由信息，并向提供Topic服务的Master建立长连接，且定时向Master发送心跳。Producer完全无状态，可集群部署。
Consumer与Name Server集群中的其中一个节点（随机选择）建立长连接，定期从Name Server取Topic路由信息，并向提供Topic服务的Master、Slave建立长连接，且定时向Master、Slave发送心跳。
Consumer既可以从Master订阅消息，也可以从Slave订阅消息，订阅规则由Broker配置决定。
客户端先找到NameServer, 然后通过NameServer再找到 Broker。
一个topic有多个队列，这些队列会均匀地分布在不同的broker服务器上。RocketMQ队列的概念和Kafka的分区概念是基本一致的，Kafka同一个topic的分区尽可能地分布在不同的broker上，分区副本也会分布在不同的broker上。
RocketMQ集群的slave会从master拉取数据备份，master分布在不同的broker上。
5）ActiveMQ：
支持简单集群模式，比如'主-备'，对高级集群模式支持不好。


其他MQ对比
对比方向 概要
吞吐量 万级的 ActiveMQ 和 RabbitMQ 的吞吐量（ActiveMQ 的性能最差）要比 十万级甚至是百万级的 RocketMQ 和 Kafka 低一个数量级。
可用性 都可以实现高可用。ActiveMQ 和 RabbitMQ 都是基于主从架构实现高可用性。RocketMQ 基于分布式架构。 kafka 也是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用
时效性 RabbitMQ 基于erlang开发，所以并发能力很强，性能极其好，延时很低，达到微秒级。其他三个都是 ms 级。
功能支持 除了 Kafka，其他三个功能都较为完备。 Kafka 功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用，是事实上的标准
消息丢失 ActiveMQ 和 RabbitMQ 丢失的可能性非常低， RocketMQ 和 Kafka 理论上不会丢失。
总结：
ActiveMQ 的社区算是比较成熟，但是较目前来说，ActiveMQ 的性能比较差，而且版本迭代很慢，不推荐使用。基于JMS实现。
RabbitMQ 在吞吐量方面虽然稍逊于 Kafka 和 RocketMQ ，但是由于它基于 erlang 开发，所以并发能力很强，性能极其好，延时很低，达到微秒级。但是也因为 RabbitMQ 基于 erlang 开发，所以国内很少有公司有实力做erlang源码级别的研究和定制。如果业务场景对并发量要求不是太高（十万级、百万级），那这四种消息队列中，RabbitMQ 一定是你的首选。如果是大数据领域的实时计算、日志采集等场景，用 Kafka 是业内标准的，绝对没问题，社区活跃度很高，绝对不会黄，何况几乎是全世界这个领域的事实性规范。基于 AMQP 协议实现的。本身支持很多其他协议：XMPP, SMTP,STOMP。
RocketMQ 阿里出品，Java 系开源项目，源代码我们可以直接阅读，然后可以定制自己公司的MQ，并且 RocketMQ 有阿里巴巴的实际业务场景的实战考验。RocketMQ 社区活跃度相对较为一般，不过也还可以，文档相对来说简单一些，然后接口这块不是按照标准 JMS 规范走的有些系统要迁移需要修改大量代码。还有就是阿里出台的技术，你得做好这个技术万一被抛弃，社区黄掉的风险，那如果你们公司有技术实力我觉得用RocketMQ 挺好的
kafka 的特点其实很明显，就是仅仅提供较少的核心功能，但是提供超高的吞吐量，ms 级的延迟，极高的可用性以及可靠性，而且分布式可以任意扩展。同时 kafka 最好是支撑较少的 topic 数量即可，保证其超高吞吐量。，那么对数据准确性会造成极其轻微的影响，在大数据领域中以及日志采集中，这点轻微影响可以忽略这个特性天然适合大数据实时计算以及日志收集。SCALA编写，基于自定义协议实现。
快速持久化：通过磁盘顺序读写与零拷贝机制，可以在O(1)的系统开销下进行消息持久化；
高吞吐：在一台普通的服务器上既可以达到10W/s的吞吐速率；
高堆积：支持topic下消费者较长时间离线，消息堆积量大；
完全的分布式系统：Broker、Producer、Consumer都原生自动支持分布式，依赖zookeeper自动实现复杂均衡；
支持Hadoop数据并行加载：对于像Hadoop的一样的日志数据和离线分析系统，但又要求实时处理的限制，这是一个可行的解决方案。

重要概念
Topic：特质Kafka处理的消息源的不同分类
Partition：Topic物理上的分组，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中每个消息都会被分配一个有序的id(offset)
创建topic时指定分区数。
Message：消息，通讯的基本单位，每个producer可以向topic发送一些信息
Producers：消息和数据生产者，可以决定数据发送到哪个分区，可以批量发送提高效率。
Consumers：消息和数据的消费者，属于某个consumer group，每个group中有多个consumer，
    发送的消息只会被订阅了该topic的consumer group中的一个consumer消费，
     一个partition中的消息只会被consumer group中的一个consumer消费。
Broker：缓存代理，kafka集群中的一台或者多台服务器，不保存订阅状态，无状态无法删除消息。

消息处理机制
发送到partitions中的数据会被按照他接收的顺序追加到日志文件中
对于消费者而言他们消费消息的顺序和日志中的消息顺序一致
如果topic的“replication factor"为N，那么允许N-1个kafka实例失败
kafka对消息的重复、丢失、错误以及顺序型没有严格要求
kafka提供at-least-once delivery即consumer宕机之后，消息有可能重复发送
因为每个partition中的数据只会被同一个consumergroup中的一个consumer消费所以kafka保证单partition内的消息顺序消费
kafka为每条消息计算CRC校验，校验出错的消息会被直接丢弃
消费者消费成功返回ack信息

持久化原理

kafka以topic进行消息管理，每个topic包含多个partition，每个partition对应一个log,每个log包含多个segment。

消息id由存储位置决定，从id可以直接定位到消息，避免额外映射

每个partition对应一个index文件，index中记录了每个segment中第一条消息的offset也就是id

消息处理流程
producer - pushmsg - topic - partition - segment tail - flush to disk (queryable) - if max - create new segment


一般来说segment最大值为1G

消息缓存时间默认一周

多维管控中使用的topic说明

topic划分规范

- 不同的业务要划分不同的topic。如IOT业务、人脸业务、门禁业务，数据告警要划分不同topic
- 告警与其他消息要划分不同topic

topic命名规范

- 名字之间单词使用中划线分割，如“xxx-iot-xxx”
- topic按照业务划分，分为物联网（iot）、人脸业务(face)、门禁业务(door)、车辆业务(vehicle)、警情警力(police)
- topic中需要表明所属的平台，方便kafka集群后多平台统一部署，应用使能平台简写aep，连接管理平台简写cmp
- 平台消息入口的topic名称以“access”开始
- 平台中的etl以“etl”开始
例子如连接管理平台的入口的IOT的数据topic，名字为cmp-access-iot-data。

group id命名规范
- 名字之间单词使用中划线分割
- group id如果一个group中带很多业务，可以使用服务器的名称，如cmp-etl

连接管理平台topic

cmp-access-iot-data
 IOT的数据，包括一般的DI、AI设备，gps设备上报的DI、AI值
cmp-access-iot-event
IOT事件，目前暂时没有数据
cmp-access-iot-alarm
IOT告警
cmp-access-face-event
人脸抓拍机的抓拍事件
cmp-access-door-event
门禁的事件，包括人脸门禁的
cmp-etl-iot-data
补全设备信息后的IOT数据
cmp-etl-iot-alarm
补全设备信息后的IOT告警
cmp-etl-face-event
补全设备信息后的人脸抓拍机事件
cmp-etl-door-event
补全设备信息后的门禁事件

数据使能平台topic

bdp-face-snap-event
人脸抓事件，包括人脸抓拍机、人脸门禁抓拍、根据视频流动态分析的事件，包括图片url，用于前端的抓拍展示
bdp-face-recg-result
- 人脸识别事件，包括bdp-face-snap-event中所有的比对结果，比对只要超过阈值，就生成比对结果，一个bdp-face-snap-event可能对应多个识别结果；
- 包括已经识别的人、未识别的陌生人
bdp-vehicle-relation-result
车辆图数据库关系建立结果队列
bdp-face-relation-result
人脸图数据库关系创建结果队列


应用使能平台

aep-operation-alarm 
包括设备、平台断线告警，写入的包括连接管理平台的接入的所有的设备、所有平台内的服务断线在线告警
aep-entity-change
基础数据变化通知队列，包括人，房，车，模型等



业务使能平台topic

bap-face-alarm 
业务使能平台的人脸告警，包括精神病人告警、吸毒人员告警、实有人口未登记等模型告警
bap-vehicle-alarm
业务使能平台的人车辆告警，包括车辆布控告警
bap-face-recg-result
补充过信息并入库之后的人脸识别结果
bap-etl-iot-alarm
入口补全后的iot告警队列
bap-etl-door-event
入库补全后的门禁事件队列
bap-vehicle-event
小区门口过车事件补全入库后的信息


KafkaTools使用
ftp地址
ftp://10.45.157.218/01-Private/kafkatool/

配置步骤
1.hosts文件修改
C:\windows\system32\drivers\etc\hosts
将linux主机的 ip+空格+主机名 的映射放入hosts文件
如下所示：

2.配置信息
File-Add New Connection
弹出如下窗口

填个name一般就是主机名就可以了
Zookeeper Host: 填主机名
端口默认
ping一下看是否能通，不通的话，检查hosts文件是否配置正确
（其他全部默认）
点击Test看是否可以连接到kafka服务器，如果成功会弹窗提示保存连接，保存即可

连接成功后如图所示


使用介绍
Brokers
服务器列表
Topics
所有的topic列表
选中topic双击，查看具体内容

如上图所示，
在Properties tab页，需要设置key和value的解码方式
默认为ByteArray，修改为String，点击Update
选中Data Tab页，点击查询

在内容显示页面，使用Text显示，为了看起来方便，这里选择View Data As JSON

保存数据并推送
如果想要推送一条告警，直接查询出一条已有告警，修改关键字段之后重新推送即可

点击上图，左上角的保存按钮，保存消息，并修改

如上图，选中左侧具体某个分区，选中右侧Data tab页
点击“+”，往这个topic的分区推送一条信息
弹窗

我们消息的key都为空，所以直接选择后面一个选项空着即可
Message字段选择From File，然后选择你保存的文件即可，点击Add推送

Consumers
订阅者列表


ES基础知识介绍
学习网站
https://www.elastic.co/guide/cn/elasticsearch/guide/current/intro.html



回忆时光
许多年前，一个刚结婚的名叫 Shay Banon 的失业开发者，跟着他的妻子去了伦敦，他的妻子在那里学习厨师。 在寻找一个赚钱的工作的时候，为了给他的妻子做一个食谱搜索引擎，他开始使用 Lucene 的一个早期版本。
       直接使用 Lucene 是很难的，因此 Shay 开始做一个抽象层，Java 开发者使用它可以很简单的给他们的程序添加搜索功能。 他发布了他的第一个开源项目 Compass。
       后来 Shay 获得了一份工作，主要是高性能，分布式环境下的内存数据网格。这个对于高性能，实时，分布式搜索引擎的需求尤为突出， 他决定重写 Compass，把它变为一个独立的服务并取名 Elasticsearch。
       第一个公开版本在2010年2月发布，从此以后，Elasticsearch 已经成为了 Github 上最活跃的项目之一，他拥有超过300名 contributors(目前736名 contributors )。 一家公司已经开始围绕 Elasticsearch 提供商业服务，并开发新的特性，但是，Elasticsearch 将永远开源并对所有人可用。
       然后，Shay 的妻子还在等着她的食谱搜索引擎…

DB-Engines Ranking of Search Engines
是什么
Elasticsearch是一个分布式的搜索和分析引擎，附带存储功能
一个分布式的实时文档存储，每个字段 可以被索引与搜索
一个分布式实时分析搜索引擎
能胜任上百个服务节点的扩展，并支持 PB 级别的结构化或者非结构化数据

特点
数据制胜，海量数据处理
系统要求高可用，7*24服务
高可扩展型
实时检索
容灾，数据安全性
数据挖掘，从大量数据淘金

核心概念
CLUSTER：集群由共享相同集群名称的一个或多个节点组成。每一个集群有一个master节点，它由集群自发确定并且在失败时，可以由新确定的master替换。
NODE：节点是属于集群的一个运行实例。
INDEX：索引类似于关系数据库中的table。索引是一个逻辑的命名空间，它映射到一个或多个主分片，并具有零个或多个副本分片。
MAPPING:类似于关系数据库中的表定义，每个索引有一个mapping和一个索引范围的settings。动态映射、静态映射。
SHARD:分片是单个lucene实例。是由elasticsearch管理的低级别的工作单元。索引是指向主和副本分片的逻辑命名空间。Elasticsearch自动将分片在集群中的所有节点分布，并且当节点失败，或者新增节点时自动将分片在节点间进行移动。
REPLIA：副本是主分片的冗余，用于查询负载均衡和故障替换。
query and filter：query是基本的检索，返回score打分值。Fillter是不带score打分的query，
查询表达式(Query DSL) 是一种非常灵活又富有表现力的 查询语言。 Elasticsearch 使用它可以以简单的 JSON 接口来展现 Lucene 功能的绝大部分。在你的应用中，你应该用它来编写你的查询语句。它可以使你的查询语句更灵活、更精确、易读和易调试。
文档：请求体查询.md

类比Mysql
ES与关系型数据库概念对比
索引：数据库
类型：表
文档：行