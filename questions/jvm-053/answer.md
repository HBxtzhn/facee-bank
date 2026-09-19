不是所有新生代和老年代收集器都能随意搭配，主要是**接口协议、内存布局、设计目标**三方面的限制。

## 1. 内存布局和分代假设不同

- 传统分代收集器（Serial、ParNew、Parallel Scavenge、CMS、Serial Old）都是基于连续内存的分代模型

- G1是基于Region的分代模型，每个Region可以是Eden、Survivor、Old，逻辑分代物理不连续

- ZGC、Shenandoah是不分代的（早期版本），整堆统一管理

- 内存模型不一样，根本没法组合——你不能让一个连续内存的新生代收集器去管理Region化的老年代

## 2. GC接口和协作机制不兼容

新生代和老年代收集器之间需要紧密配合：

- Young GC时需要老年代做分配担保

- 需要互相通知引用变化（卡表、写屏障）

- 有一套约定的接口和数据结构

比如：

- Parallel Scavenge有自己的一套框架，和CMS的框架不兼容，所以不能搭配

- ParNew是专门为CMS设计的，实现了CMS需要的接口，所以只有ParNew能和CMS配合

- Serial和Serial Old是一套，Parallel Scavenge和Parallel Old是一套，ParNew和CMS是一套

## 3. 设计目标冲突

- Parallel Scavenge主打吞吐量，CMS主打低延迟，设计目标完全不同

- 强行搭配的话，两边的优化方向不一致，整体效果反而不好

- 吞吐量优先的新生代配吞吐量优先的老年代（Parallel Scavenge + Parallel Old）效果才最好

- 低延迟的新生代配低延迟的老年代（ParNew + CMS）才协调

## 经典组合总结

|新生代收集器|可搭配的老年代收集器|
|---|---|
|Serial|Serial Old、CMS|
|ParNew|Serial Old、CMS|
|Parallel Scavenge|Serial Old、Parallel Old|
|G1|G1自己（整堆）|
|ZGC|ZGC自己（整堆）|
