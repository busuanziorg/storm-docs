---
title: 守护程序容错
layout: documentation
documentation: true
---
Storm有几个不同的守护进程。Nimbus安排workers，supervisors启动和杀死workers，日志查看器提供对日志的访问，以及显示集群状态的UI。

## worker死亡时会发生什么？

当worker（工作者）死亡时，supervisor（主管）将重新启动它。如果它在启动时连续失败并且无法对Nimbus进行心跳，Nimbus将重新安排工作人员。

## 节点死亡时会发生什么？

分配给该机器的任务将超时，Nimbus会将这些任务重新分配给其他机器。

## 当Nimbus或Supervisor守护进程死掉时会发生什么？

Nimbus和Supervisor守护进程设计为快速失败（遇到任何意外情况时进程自毁）和无状态（所有状态都保存在Zookeeper或磁盘上）。如[设置Storm群集Storm](Setting-up-a-Storm-cluster.md)中所述，Nimbus和Supervisor守护程序必须使用daemontools或monit等工具在监督下运行。因此，如果Nimbus或Supervisor守护进程死亡，它们会重新启动，就像没有发生任何事情一样。

最值得注意的是，Nimbus或Supervisors的死亡不会影响worker流程。这与Hadoop形成鲜明对比，如果JobTracker死亡，所有正在运行的作业都将丢失。

## Nimbus是单点故障吗？

如果丢失了Nimbus节点，worker仍将继续运行。此外，如果worker死亡，Supervisors（主管）将继续重新启动worker。但是，如果没有Nimbus，worker将不会在必要时重新分配给其他计算机（例如，如果您丢失了worker机器）。

Storm Nimbus自1.0.0以来是高可用的。更多信息请参考[Nimbus高可用设计](nimbus-ha-design.md)文档。

##  Storm如何保证数据处理？

即使节点死亡或消息丢失，Storm也提供保证数据处理的机制。有关详细信息，请参见[保证消息处理](Guaranteeing-message-processing.md)。
