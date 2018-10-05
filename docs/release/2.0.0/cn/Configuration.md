---
title: 配置
layout: documentation
documentation: true
---
Storm有各种配置来调整nimbus，supervisors和运行中拓扑的行为。某些配置是系统配置，无法通过拓扑基础在拓扑上进行修改，而其他配置可以按拓扑进行修改。


每个配置都有一个默认值，在Storm codebase中的[defaults.yaml]({{page.git-blob-base}}/conf/defaults.yaml)中定义。您可以通过在Nimbus和supervisors的类路径中定义storm.yaml来覆盖这些配置。最后，您可以定义在使用[StormSubmitter](javadocs/org/apache/storm/StormSubmitter.md)时与拓扑一起提交的特定于拓扑的配置。但是，特定于拓扑的配置只能覆盖前缀为“拓扑”的配置。

Storm 0.7.0及更高版本允许您基于每个bolt/每个spout的基础上覆盖配置。可以通过这种方式覆盖的唯一配置是：

1. "topology.debug"
2. "topology.max.spout.pending"
3. "topology.max.task.parallelism"
4. "topology.kryo.register": 这与其他组件的工作方式略有不同，因为序列化将可用于拓扑中的所有组件。有关[序列化](Serialization.md)的更多详细信息。

Java API允许您以两种方式指定组件特定的配置：

1. *内部:* 在任何spout或bolt中覆盖`getComponentConfiguration`并返回特定于组件的配置map。
2. *外部:* `topSpilder`中的`setSpout`和`setBolt`返回一个带有方法`addConfiguration`和`addConfigurations`的对象，可以用来覆盖组件的配置。

配置值的首选顺序是defaults.yaml < storm.yaml < 拓扑特定配置 < 内部组件特定配置 < 外部组件特定配置。

# Bolts，Spouts和插件
在几乎所有情况下，bolt或spout的配置都应该通过bolt或spout实施的设置器来完成，而不是拓扑配置。在某些罕见的情况下，它可能会成为
暴露拓扑宽配置，这些配置目前不是[Config](javadocs/org/apache/storm/Config.md) 或[DaemonConfig](javadocs/org/apache/storm/DaemonConfig.md)的一部分，例如
在为storm的某些部分编写自定义调度程序或插件时。在那些您可以创建自己的类，例如Config，但实现[[Validated](javadocs/org/apache/storm/validation/Validated.md)。在此类声明的任何 `public static final String`字段将被视为配置，并且来自`org.apache.storm.validation.ConfigValidationAnnotations` 类的注释可用于强制执行该配置中存储的内容。
为了让验证器了解这个类，你需要将类视为一个服务，它将通过ServiceLoader 为Validated类加载，并包含一个`META-INF/services/org.apache.storm.validation.Validated`文件在你的jar中，它包含你的Config类的名字。

**资源:**

* [配置](javadocs/org/apache/storm/Config.md): 客户端配置列表以及用于创建拓扑特定配置的帮助类
* [DaemonConfig](javadocs/org/apache/storm/DaemonConfig.md): Storm守护程序配置列表。
* [defaults.yaml]({{page.git-blob-base}}/conf/defaults.yaml): 所有配置的默认值
* [设置Storm集群](Setting-up-a-Storm-cluster.md): 解释如何创建和配置Storm集群
* [在生产集群集上运行拓扑](Running-topologies-on-a-production-cluster.md): 列出在群集上运行拓扑时的有用配置
* [本地模式](Local-mode.md): 列出使用本地模式时的有用配置
