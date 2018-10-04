---
title: Documentation
layout: documentation
documentation: true
---
### Basics of Storm

* [Javadoc](javadocs/index.md)
* [Tutorial](Tutorial.md)
* [Concepts](Concepts.md)
* [Scheduler](Storm-Scheduler.md)
* [Configuration](Configuration.md)
* [Guaranteeing message processing](Guaranteeing-message-processing.md)
* [Daemon Fault Tolerance](Daemon-Fault-Tolerance.md)
* [Command line client](Command-line-client.md)
* [REST API](STORM-UI-REST-API.md)
* [Understanding the parallelism of a Storm topology](Understanding-the-parallelism-of-a-Storm-topology.md)
* [FAQ](FAQ.md)

### Layers on top of Storm

#### Trident

Trident is an alternative interface to Storm. It provides exactly-once processing, "transactional" datastore persistence, and a set of common stream analytics operations.

* [Trident Tutorial](Trident-tutorial.md)     -- basic concepts and walkthrough
* [Trident API Overview](Trident-API-Overview.md) -- operations for transforming and orchestrating data
* [Trident State](Trident-state.md)        -- exactly-once processing and fast, persistent aggregation
* [Trident spouts](Trident-spouts.md)       -- transactional and non-transactional data intake
* [Trident RAS API](Trident-RAS-API.md)     -- using the Resource Aware Scheduler with Trident.

#### Streams API

Stream APIs is another alternative interface to Storm. It provides a typed API for expressing streaming computations and supports functional style operations.

NOTE: Streams API is an `experimental` feature, and further works might break backward compatibility.
We're also notifying it via annotating classes with marker interface `@InterfaceStability.Unstable`.

* [Streams API](Stream-API.md)

#### SQL

The Storm SQL integration allows users to run SQL queries over streaming data in Storm.  

NOTE: Storm SQL is an `experimental` feature, so the internals of Storm SQL and supported features are subject to change.
But small change will not affect the user experience. We will notify the user when breaking UX change is introduced.

* [Storm SQL overview](storm-sql.md)
* [Storm SQL example](storm-sql-example.md)
* [Storm SQL reference](storm-sql-reference.md)
* [Storm SQL internal](storm-sql-internal.md)

#### Flux

* [Flux Data Driven Topology Builder](flux.md)

### Setup and Deploying

* [Setting up a Storm cluster](Setting-up-a-Storm-cluster.md)
* [Local mode](Local-mode.md)
* [Troubleshooting](Troubleshooting.md)
* [Running topologies on a production cluster](Running-topologies-on-a-production-cluster.md)
* [Building Storm](Maven.md) with Maven
* [Setting up a Secure Cluster](SECURITY.md)
* [CGroup Enforcement](cgroups_in_storm.md)
* [Pacemaker reduces load on zookeeper for large clusters](Pacemaker.md)
* [Resource Aware Scheduler](Resource_Aware_Scheduler_overview.md)
* [Daemon Metrics/Monitoring](storm-metrics-profiling-internal-actions.md)
* [Windows users guide](windows-users-guide.md)
* [Classpath handling](Classpath-handling.md)

### Intermediate

* [Serialization](Serialization.md)
* [Common patterns](Common-patterns.md)
* [DSLs and multilang adapters](DSLs-and-multilang-adapters.md)
* [Using non-JVM languages with Storm](Using-non-JVM-languages-with-Storm.md)
* [Distributed RPC](Distributed-RPC.md)
* [Hooks](Hooks.md)
* [Metrics (Deprecated)](Metrics.md)
* [Metrics V2](metrics_v2.md)
* [State Checkpointing](State-checkpointing.md)
* [Windowing](Windowing.md)
* [Joining Streams](Joins.md)
* [Blobstore(Distcahce)](distcache-blobstore.md)

### Debugging
* [Dynamic Log Level Settings](dynamic-log-level-settings.md)
* [Searching Worker Logs](Logs.md)
* [Worker Profiling](dynamic-worker-profiling.md)
* [Event Logging](Eventlogging.md)

### Integration With External Systems, and Other Libraries
* [Apache Kafka Integration](storm-kafka-client.md)
* [Apache HBase Integration](storm-hbase.md)
* [Apache HDFS Integration](storm-hdfs.md)
* [Apache Hive Integration](storm-hive.md)
* [Apache Solr Integration](storm-solr.md)
* [Apache Cassandra Integration](storm-cassandra.md)
* [Apache RocketMQ Integration](storm-rocketmq.md)
* [JDBC Integration](storm-jdbc.md)
* [JMS Integration](storm-jms.md)
* [MQTT Integration](storm-mqtt.md)
* [Redis Integration](storm-redis.md)
* [Event Hubs Intergration](storm-eventhubs.md)
* [Elasticsearch Integration](storm-elasticsearch.md)
* [Mongodb Integration](storm-mongodb.md)
* [OpenTSDB Integration](storm-opentsdb.md)
* [Kinesis Integration](storm-kinesis.md)
* [PMML Integration](storm-pmml.md)
* [Kestrel Integration](Kestrel-and-Storm.md)

#### Container, Resource Management System Integration

* [YARN Integration](https://github.com/yahoo/storm-yarn)
* [Mesos Integration](https://github.com/mesos/storm)
* [Docker Integration](https://hub.docker.com/_/storm/)
* [Kubernetes Integration](https://github.com/kubernetes/examples/tree/master/staging/storm)

### Advanced

* [Defining a non-JVM language DSL for Storm](Defining-a-non-jvm-language-dsl-for-storm.md)
* [Multilang protocol](Multilang-protocol.md) (how to provide support for another language)
* [Implementation docs](Implementation-docs.md)
* [Storm Metricstore](storm-metricstore.md)
