垃圾收集器是GC算法的具体实现，不同收集器适用不同场景。按分代和发展历程可以分为以下几类：

## 新生代收集器

**1. Serial（串行收集器）**

- 单线程收集，收集时暂停所有用户线程（STW）

- 简单高效，Client模式下的默认新生代收集器

- 采用复制算法

**2. ParNew（并行收集器）**

- Serial的多线程版本，多条GC线程并行回收

- 除了多线程，其他和Serial几乎一样

- 只有它能和CMS配合使用

- 采用复制算法

**3. Parallel Scavenge（吞吐量优先收集器）**

- 多线程并行收集，目标是达到可控制的吞吐量

- 也叫"吞吐量优先收集器"，适合后台计算任务

- 采用复制算法

- Java8默认的新生代收集器

## 老年代收集器

**4. Serial Old（串行老年代）**

- 单线程，标记-整理算法

- 主要作为CMS的后备方案，CMS失败时兜底用

**5. Parallel Old（并行老年代）**

- Parallel Scavenge的老年代版本

- 多线程并行，标记-整理算法

- 和Parallel Scavenge搭配，吞吐量优先组合

**6. CMS（Concurrent Mark Sweep）**

- 并发低延迟收集器，以最短STW时间为目标

- 标记-清除算法

- 适合互联网应用、B/S系统的服务端

- Java8及之前的主流低延迟收集器

## 整堆收集器（不分代）

**7. G1（Garbage First）**

- 面向服务端，兼顾吞吐量和低延迟

- 把堆分成多个Region，可预测停顿时间

- Java9之后的默认收集器

**8. ZGC（Z Garbage Collector）**

- 超低延迟收集器，停顿时间不超过10ms

- 染色指针技术，几乎所有阶段都并发

- Java11正式引入，Java15生产就绪

**9. Shenandoah**

- 类似ZGC的低延迟收集器，RedHat开发

- 停顿时间和堆大小无关

## 收集器组合关系

|新生代|老年代|特点|
|---|---|---|
|Serial|Serial Old|单线程，客户端模式|
|ParNew|CMS|低延迟，Java8前主流|
|Parallel Scavenge|Parallel Old|吞吐量优先，Java8默认|
|G1|G1|整堆，可预测停顿，Java9+默认|
|ZGC|ZGC|整堆，超低延迟，Java15+|
