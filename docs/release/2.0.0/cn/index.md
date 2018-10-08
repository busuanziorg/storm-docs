---
title: 文档
layout: documentation
documentation: true
---
### Storm的基础知识

* [Javadoc](http://storm.apache.org/releases/2.0.0-SNAPSHOT/javadocs/index.html)
* [教程](Tutorial.md)
* [概念](Concepts.md)
* [调度](Storm-Scheduler.md)
* [配置](Configuration.md)
* [保证消息处理](Guaranteeing-message-processing.md)
* [守护程序容错](Daemon-Fault-Tolerance.md)
* [命令行客户端](Command-line-client.md)
* [REST API](STORM-UI-REST-API.md)
* [了解Storm拓扑的并行性](Understanding-the-parallelism-of-a-Storm-topology.md)
* [FAQ](FAQ.md)

### Storm顶层设计

#### Trident

Trident 是Storm的另一种接口。 它提供一次性处理，“事务性”数据存储持久性和一组常见的流分析操作。

* [Trident 教程](Trident-tutorial.md)     -- 基本概念和演练。
* [Trident API Overview](Trident-API-Overview.md) -- 用于转换和编排数据的操作。
* [Trident 状态(State)](Trident-state.md)        -- 完全一次处理和快速，持久的聚合。
* [Trident spouts](Trident-spouts.md)       -- 交易和非交易数据。
* [Trident RAS API](Trident-RAS-API.md)     -- 将资源感知调度程序与Trident一起使用。

#### 流(Streams) API

流 APIs 是Storm的另一种替代接口。 它提供了一种用于表达流式计算的类型化API，并支持功能样式操作。

注意：流 API 是一种“实验性”功能，进一步的工作可能会破坏向后兼容性。
我们还通过使用标记接口`@InterfaceStability.Unstable`注释类来通知它。

* [流 API](Stream-API.md)

#### SQL

Storm SQL集成允许用户在Storm中通过流数据运行SQL查询。

注意：Storm SQL是一个“实验性”功能，因此Storm SQL的内部和支持的功能可能会发生变化。
但小的改变不会影响用户体验。 我们将在引入UX更改时通知用户。

* [Storm SQL 概述](storm-sql.md)
* [Storm SQL 示例](storm-sql-example.md)
* [Storm SQL 参考](storm-sql-reference.md)
* [Storm SQL 内部](storm-sql-internal.md)

#### Flux

* [Flux 数据驱动的拓扑生成器](flux.md)

### 设置和部署

* [设置Storm群集](Setting-up-a-Storm-cluster.md)
* [本地模式](Local-mode.md)
* [故障排除](Troubleshooting.md)
* [在生产集群上运行拓扑](Running-topologies-on-a-production-cluster.md)
* 基于Maven[构建 Storm](Maven.md)
* [设置安全群集](SECURITY.md)
* [CGroup 实施](cgroups_in_storm.md)
* [Pacemaker 减少动物园管理员对大型集群的负担](Pacemaker.md)
* [资源感知调度程序](Resource_Aware_Scheduler_overview.md)
* [守护程序度量/监控](storm-metrics-profiling-internal-actions.md)
* [Windows 用户手册](windows-users-guide.md)
* [类路径处理](Classpath-handling.md)

### 中级

* [序列化](Serialization.md)
* [常见模式](Common-patterns.md)
* [DSLs和multilang适配器](DSLs-and-multilang-adapters.md)
* [使用带Storm的非JVM语言](Using-non-JVM-languages-with-Storm.md)
* [分布式 RPC](Distributed-RPC.md)
* [挂钩](Hooks.md)
* [指标 (已弃用)](Metrics.md)
* [指标 V2](metrics_v2.md)
* [State 检查点](State-checkpointing.md)
* [Windowing](Windowing.md)
* [加入 Streams](Joins.md)
* [Blobstore(Distcahce)](distcache-blobstore.md)

### 调试
* [动态日志级别设置](dynamic-log-level-settings.md)
* [搜索Worker日志](Logs.md)
* [Worker 剖析](dynamic-worker-profiling.md)
* [事件记录](Eventlogging.md)

### 与外部系统和其他库集成
* [Apache Kafka 集成](storm-kafka-client.md)
* [Apache HBase 集成](storm-hbase.md)
* [Apache HDFS 集成](storm-hdfs.md)
* [Apache Hive 集成](storm-hive.md)
* [Apache Solr 集成](storm-solr.md)
* [Apache Cassandra 集成](storm-cassandra.md)
* [Apache RocketMQ 集成](storm-rocketmq.md)
* [JDBC 集成](storm-jdbc.md)
* [JMS 集成](storm-jms.md)
* [MQTT 集成](storm-mqtt.md)
* [Redis 集成](storm-redis.md)
* [Event Hubs 集成](storm-eventhubs.md)
* [Elasticsearch 集成](storm-elasticsearch.md)
* [Mongodb 集成](storm-mongodb.md)
* [OpenTSDB 集成](storm-opentsdb.md)
* [Kinesis 集成](storm-kinesis.md)
* [PMML 集成](storm-pmml.md)
* [Kestrel 集成](Kestrel-and-Storm.md)

#### 容器，资源管理系统集成

* [YARN 集成](https://github.com/yahoo/storm-yarn)
* [Mesos 集成](https://github.com/mesos/storm)
* [Docker 集成](https://hub.docker.com/_/storm/)
* [Kubernetes 集成](https://github.com/kubernetes/examples/tree/master/staging/storm)

### 高级

* [为Storm定义非JVM语言DSL](Defining-a-non-jvm-language-dsl-for-storm.md)
* [Multilang 协议](Multilang-protocol.md) (如何为另一种语言提供支持)
* [实施文档](Implementation-docs.md)
* [Storm Metricstore](storm-metricstore.md)
