---
title: Trident RAS API
layout: documentation
documentation: true
---

## Trident RAS API

The Trident RAS (Resource Aware Scheduler) API provides a mechanism to allow users to specify the resource consumption of a Trident topology. The API looks exactly like the base RAS API, only it is called on Trident Streams instead of Bolts and Spouts.

In order to avoid duplication and inconsistency in documentation, the purpose and effects of resource setting are not described here, but are instead found in the [Resource Aware Scheduler Overview](Resource_Aware_Scheduler_overview.md)

### Use

First, an example:

```java
    TridentTopology topo = new TridentTopology();
    topo.setResourceDefaults(new DefaultResourceDeclarer();
                                                          .setMemoryLoad(128)
                                                          .setCPULoad(20));
    TridentState wordCounts =
        topology
            .newStream("words", feeder)
            .parallelismHint(5)
            .setCPULoad(20)
            .setMemoryLoad(512,256)
            .each( new Fields("sentence"),  new Split(), new Fields("word"))
            .setCPULoad(10)
            .setMemoryLoad(512)
            .each(new Fields("word"), new BangAdder(), new Fields("word!"))
            .parallelismHint(10)
            .setCPULoad(50)
            .setMemoryLoad(1024)
            .each(new Fields("word!"), new QMarkAdder(), new Fields("word!?"))
            .groupBy(new Fields("word!"))
            .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
            .setCPULoad(100)
            .setMemoryLoad(2048);
```

Resources can be set for each operation (except for grouping, shuffling, partitioning).
Operations that are combined by Trident into single Bolts will have their resources summed.

Every Bolt is given **at least** the default resources, regardless of user settings.

In the above case, we end up with


- a spout and spout coordinator with a CPU load of 20% each, and a memory load of 512MiB on-heap and 256MiB off-heap.
- a bolt with 80% cpu load (10% + 50% + 20%) and a memory load of 1664MiB (1024 + 512 + 128) on-heap from the combined `Split` and `BangAdder` and the `QMarkAdder` which used the default resources contained in the DefaultResourceDeclarer
- a bolt with 100% cpu load and a memory load of 2048MiB on-heap, with default value for off-heap.

Resource declarations may be called after any operation. The operations without explicit resources will get the defaults. If you choose to set resources for only some operations, defaults must be declared, or topology submission will fail.
Resource declarations have the same *boundaries* as parallelism hints. They don't cross any groupings, shufflings, or any other kind of repartitioning.
Resources are declared per operation, but get combined within boundaries.
