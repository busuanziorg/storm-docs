---
title: 调度
layout: documentation
documentation: true
---

Storm现在有4种内置调度程序：[默认调度（DefaultScheduler）]({{page.git-blob-base}}/storm-server/src/main/java/org/apache/storm/scheduler/DefaultScheduler.java), [隔离调度（IsolationScheduler）]({{page.git-blob-base}}/storm-server/src/main/java/org/apache/storm/scheduler/IsolationScheduler.java), [多租户调度（MultitenantScheduler）]({{page.git-blob-base}}/storm-server/src/main/java/org/apache/storm/scheduler/multitenant/MultitenantScheduler.java), [资源感知调度（ResourceAwareScheduler）](Resource_Aware_Scheduler_overview.md).

## 可插拔调度
您可以实现自己的调度程序来替换默认调度程序，以将执行程序分配给工作者。您将类配置为在storm.yaml中使用“storm.scheduler”配置，并且您的调度程序必须实现 [IScheduler]({{page.git-blob-base}}/storm-client/src/jvm/org/apache/storm/scheduler/IScheduler.java)接口.

## 隔离调度
隔离调度使得在许多拓扑中共享集群变得简单而安全。隔离调度允许您指定哪些拓扑应该“隔离”，这意味着它们在群集内的一组专用计算机上运行，​​其中不会运行其他拓扑。这些隔离拓扑在集群上具有优先权，因此如果存在与非隔离拓扑的竞争，则将资源分配给隔离拓扑，并且如果需要，将从非隔离拓扑中获取资源以获得用于隔离拓扑的资源。分配所有隔离的拓扑后，群集中的其余计算机将在所有非隔离拓扑中共享。

您可以通过将“storm.scheduler”设置为“org.apache.storm.scheduler.IsolationScheduler”来在Nimbus配置中配置隔离调度程序。然后，使用“isolation.scheduler.machines”配置来指定每个拓扑应该获得多少台机器。此配置是从拓扑名称到分配给此拓扑的隔离计算机数量的映射。例如：

```
isolation.scheduler.machines:
    "my-topology": 8
    "tiny-topology": 1
    "some-other-topology": 3
```

提交给未列在其中的群集的任何拓扑都不会被隔离。请注意，Storm的用户无法影响其隔离设置 - 这只能由群集管理员允许（这非常有意）。


隔离调度程序通过在拓扑之间提供完全隔离来解决多租户问题 - 避免拓扑之间的资源争用。目的是“生产的”拓扑应该在隔离配置中列出，测试或开发中的拓扑不应该。群集中的其余计算机为隔离拓扑和运行非隔离拓扑提供故障转移的双重角色。
