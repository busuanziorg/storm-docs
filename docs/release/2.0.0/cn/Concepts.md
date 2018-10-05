---
title: 概念
layout: documentation
documentation: true
---

此页面列出了Storm的主要概念以及可以找到更多信息的资源链接。讨论的概念是：

1. 拓扑（Topologies）
2. 流（Streams）
3. Spouts
4. Bolts
5. 流分组（Stream groupings）
6. 可靠性（Reliability）
7. 任务（Tasks）
8. 工作者（Workers）

### 拓扑

实时应用程序的逻辑被打包到Storm拓扑中。 Storm拓扑类似于MapReduce作业。一个关键的区别是MapReduce作业最终完成，而拓扑结构永远运行（当然，直到你杀死它）。拓扑是与流分组连接的喷口和螺栓的图形。这些概念如下所述。

**资源:**

* [TopologyBuilder](javadocs/org/apache/storm/topology/TopologyBuilder.md): 使用此类在Java中构建拓扑
* [在生产集群上运行拓扑](Running-topologies-on-a-production-cluster.md)
* [本地模式](Local-mode.md): 阅读本文以了解如何在本地模式下开发和测试拓扑。

### 流（Streams）

流是Storm中的核心抽象。流是无限的元组序列，以分布式方式并行处理和创建。流定义了一个模式，该模式命名流的元组中的字段。默认情况下，元组可以包含整数，长整数，短整数，字节，字符串，双精度数，浮点数，布尔值和字节数组。您还可以定义自己的序列化程序，以便可以在元组中本机使用自定义类型。

声明时，每个流都被赋予一个id。由于单流spout和bolt非常常见，[OutputFieldsDeclarer](javadocs/org/apache/storm/topology/OutputFieldsDeclarer.md)具有方便的方法来声明单个流而不指定id。在这种情况下，流的默认ID为“default”。


**资源:**

* [元组](javadocs/org/apache/storm/tuple/Tuple.md): 流由元组组成
* [OutputFieldsDeclarer](javadocs/org/apache/storm/topology/OutputFieldsDeclarer.md): 用于声明流及其模式
* [序列化](Serialization.md): 有关Storm的元组动态类型和声明自定义序列化的信息

### Spouts

spout是拓扑中的流的来源。通常，spouts将从外部源读取元组并将它们发送到拓扑中（例如，Kestrel队列或Twitter API）。 Spout可以是 __可靠的__ 或 __不可靠的__ 。如果一个元组无法被Storm处理，那么一个可靠的spout能够重放一个元组，而一个不可靠的spout一旦发出就会忘记元组。

Spouts可以发出多个流。为此，使用[OutputFieldsDeclarer](javadocs/org/apache/storm/topology/OutputFieldsDeclarer.md)的`declareStream`方法声明多个流，并在[SpoutOutputCollector](javadocs/org/apache/storm/spout/SpoutOutputCollector.md)上使用`emit`方法指定要发出的流。

Spouts的主要方法是`nextTuple`。 `nextTuple`要么在拓扑中发出新的元组，要么在没有新的元组要发出时返回。 “nextTuple”必须阻止任何spout实现，因为Storm会在同一个线程上调用所有spout方法。

spouts另一个主要的方法是 `ack` 和 `fail`. 当Storm检测到从喷口发出的元组通过拓扑成功完成或未能完成时，会调用这些元素。`ack`和`fail`只被称为可靠的spouts。有关更多信息，请参阅[the Javadoc](javadocs/org/apache/storm/spout/ISpout.md)。

**资源:**

* [IRichSpout](javadocs/org/apache/storm/topology/IRichSpout.md): 这是spouts必须实现的接口。
* [保证消息处理](Guaranteeing-message-processing.md)

### Bolts

拓扑中的所有处理都是用bolts完成的。 Bolts可以执行任何操作，包括过滤，函数，聚合，连接，与数据库交互等。

Bolts可以进行简单的流转换。进行复杂的流转换通常需要多个步骤，因此需要多个bolts。例如，将推文流转换为趋势图像流至少需要两个步骤：一个螺栓用于为每个图像执行转推滚动计数，一个或多个bolts用于流出顶部X图像（您可以执行此操作）使用三个bolts而不是两个bolts以更可扩展的方式进行特定的流转换。

Bolts可以发出多个流。为此，使用[OutputFieldsDeclarer](javadocs/org/apache/storm/topology/OutputFieldsDeclarer.md)的`declareStream`声明多个流，并在[OutputCollector](javadocs/org/apache/storm/task/OutputCollector.md)上使用`emit`方法时指定要发出的流。

声明bolt的输入流时，总是订阅另一个组件的特定流。如果要订阅另一个组件的所有流，则必须单独订阅每个组件。[InputDeclarer](javadocs/org/apache/storm/topology/InputDeclarer.md) 具有语法糖，用于订阅在默认流ID上声明的流。写`declarer.shuffleGrouping("1")`来订阅组件“1”上的默认流，相当于`declarer.shuffleGrouping("1", DEFAULT_STREAM_ID)`.

Bolts中的主要方法是`execute`方法，它接收一个新的元组作为输入。 Bolts使用[OutputCollector](javadocs/org/apache/storm/task/OutputCollector.md)对象发出新元组。对于他们处理的每个元组，Bolts必须在`OutputCollector`上调用`ack`方法，以便Storm知道元组何时完成（并且最终可以确定它是否可以安全地`ack`原始的spout元组）。对于处理输入元组，基于该元组发出0或更多元组，然后对输入元组进行调整的常见情况，Storm提供了[IBasicBolt](javadocs/org/apache/storm/topology/IBasicBolt.md)接口，自动执行acking任务。

它非常适合在“bolts”中启动异步处理的新线程。[OutputCollector](javadocs/org/apache/storm/task/OutputCollector.md)是线程安全的，可以随时调用。

**资源:**

* [IRichBolt](javadocs/org/apache/storm/topology/IRichBolt.md): 这是bolts的通用接口。
* [IBasicBolt](javadocs/org/apache/storm/topology/IBasicBolt.md): 这是一个方便的接口，用于定义执行过滤或简单功能的bolts。
* [OutputCollector](javadocs/org/apache/storm/task/OutputCollector.md): bolts使用此类的实例将元组发送到其输出流。
* [保证消息处理](Guaranteeing-message-processing.md)

### 流分组

定义拓扑的一部分是为每个“bolts”指定它应该作为输入接收的流。流分组定义了如何在bolts的任务中对该流进行分区。

Storm中有八个内置流分组，您可以通过实现[CustomStreamGrouping](javadocs/org/apache/storm/grouping/CustomStreamGrouping.md)接口来实现自定义流分组：

1. **混洗分组（Shuffle grouping）**: 元组随机分布在bolts的任务中，使得每个bolts都能保证获得相同数量的元组。
2. **字段分组（Fields grouping）**: 流由分组中指定的字段分区。例如，如果流按“user-id”字段分组，则具有相同“user-id”的元组将始终执行相同的任务，但具有不同“user-id”的元组可能会执行不同的任务。
3. **部分键分组（Partial Key grouping）**: 流按分组中指定的字段进行分区，如字段分组，但在两个下游螺栓之间进行负载平衡，这可在传入数据倾斜时提供更好的资源利用率。 [本文](https://melmeric.files.wordpress.com/2014/11/the-power-of-both-choices-practical-load-balancing-for-distributed-stream-processing-engines.pdf) 提供很好地解释它是如何工作的以及它提供的优势。
4. **全部分组（All grouping）**: 流被复制到所有bolts的任务中。小心使用此分组。
5. **全局分组（Global grouping）**: 整个流程转到了一个bolts的任务中。具体来说，它转到id最低的任务。
6. **无分组（None grouping）**: 此分组指定您不关心流的分组方式。目前，无分组相当于混洗分组。最终，Storm会使得无分组的bolts，在与他们订购的bolts或spout相同的线程中执行（如果可能的话）。
7. **直接分组（Direct grouping）**: 这是一种特殊的分组。以这种方式分组的流意味着元组的 __生产者__ 决定消费者的哪个任务将接收此元组。 直接分组只能在已声明为直接流的流上声明。发送到直接流的元组必须使用[emitDirect](javadocs/org/apache/storm/task/OutputCollector.md#emitDirect(int, int, java.util.List)之一方法发出。一个bolt可以得到通过使用提供的[TopologyContext](javadocs/org/apache/storm/task/TopologyContext.md)或跟踪[OutputCollector](javadocs/org/apache/storm/task/OutputCollector.md) 中的`emit`方法的输出来获取其消费者的任务ID（返回元组发送到的任务ID）。
8. **本地或混洗分组（Local or shuffle grouping）**: 如果目标bolt在同一个工作者进程中有一个或多个任务，则元组将被混洗到只有那些在进程内部（in-process）的任务。否则，这就像一个普通的混洗分组。

**资源:**

* [TopologyBuilder](javadocs/org/apache/storm/topology/TopologyBuilder.md): 使用此类定义拓扑
* [InputDeclarer](javadocs/org/apache/storm/topology/InputDeclarer.md): 只要在`TopologyBuilder`上调用`setBolt`并且用于声明bolt的输入流以及如何对这些流进行分组，就会返回此对象

### 可靠性

Storm保证每个spout元组都将由拓扑完全处理。它通过跟踪每个spout元组触发的元组树并确定元组树何时成功完成来实现。每个拓扑都有一个与其关联的“消息超时”。如果Storm未能检测到在该超时内已完成一个spout元组，则它会使元组失败并在以后重播。

要利用Storm的可靠性功能，必须在创建元组树中的新边时告诉Storm，并在完成处理单个元组时告诉Storm。这些是使用[OutputCollector](javadocs/org/apache/storm/task/OutputCollector.md)对象完成的，该对象用于发出元组。锚定是在`emit`方法中完成的，你声明你已经使用`ack`方法完成了一个元组。

这些都在[保证消息处理](Guaranteeing-message-processing.md)中进行了更详细的解释。

### 任务

每个spout或bolt在整个集群中执行任意数量的任务。每个任务对应一个执行线程，流分组定义如何将元组从一组任务发送到另一组任务。您可以在[TopologyBuilder](javadocs/org/apache/storm/topology/TopologyBuilder.md)的`setSpout`和`setBolt`方法中为每个spout或bolt设置并行度。

### 工作者

拓扑在一个或多个工作者进程中执行。每个工作者进程都是物理JVM，并执行拓扑的所有任务的子集。例如，如果拓扑的组合并行度为300且分配了50个工作者，则每个工作者将执行6个任务（作为工作者中的线程）。Storm试图在所有工作者之间平均分配任务。

**资源:**

* [Config.TOPOLOGY_WORKERS](javadocs/org/apache/storm/Config.md#TOPOLOGY_WORKERS): 此配置设置为执行拓扑而分配的工作者数量

### 性能调优

请参阅[性能调整指南](Performance.md)
