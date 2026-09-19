Full GC是对整个堆（新生代+老年代+方法区）的回收，STW时间长，对业务影响大，是调优的重点关注对象。

## 触发Full GC的常见场景

**1. 老年代空间不足**

- 最常见的触发原因

- 新生代晋升的对象老年代放不下，或者大对象直接分配到老年代时空间不够

- 触发Full GC回收整个堆

**2. 元空间（永久代）空间不足**

- 加载的类太多，元空间达到MaxMetaspaceSize上限

- 触发Full GC尝试卸载没用的类

**3. 显式调用System.gc()**

- 代码里调用`System.gc()`或者`Runtime.getRuntime().gc()`

- 只是建议JVM GC，不一定立刻执行，但大多数情况会触发Full GC

- 可以用`-XX:+DisableExplicitGC`禁止显式GC

**4. CMS的Concurrent Mode Failure**

- CMS并发回收过程中，用户线程又产生了大量对象，老年代不够用了

- 并发模式失败，CMS退化为Serial Old单线程Full GC

- 这是CMS最严重的问题之一，STW时间很长

**5. 晋升担保失败**

- Young GC前检查老年代空间，发现历次平均晋升大小都比老年代剩余空间大

- 担保不通过，直接触发Full GC

**6. 大对象直接分配老年代失败**

- 超过PretenureSizeThreshold的大对象直接进老年代

- 老年代连续空间不够，触发Full GC

**7. 统计数据认为Young GC后晋升会失败**

- JVM根据历史晋升数据预测这次Young GC后老年代可能放不下

- 保守起见先做一次Full GC
