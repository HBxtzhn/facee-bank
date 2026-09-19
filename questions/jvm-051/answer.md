**Concurrent Mode Failure（并发模式失败）**是CMS收集器特有的严重问题，发生在CMS并发回收过程中。

## 什么是Concurrent Mode Failure

CMS是并发收集器，大部分GC阶段和用户线程同时运行。但如果：

- CMS正在并发标记/清理的过程中

- 用户线程还在不停创建新对象、往老年代晋升对象

- 老年代空间突然被占满了，CMS还没清理完

- 这时候新对象要进老年代但没空间了

就发生了 **Concurrent Mode Failure**。

## 发生后的后果

1. CMS立即停止并发回收

2. 退化为 **Serial Old** 单线程老年代收集器

3. 触发长时间的Full GC，全程STW

4. STW时间可能从几十毫秒飙升到几秒甚至几十秒，对业务影响极大

## 常见触发原因

1. **老年代空间不足**：堆太小，或者存活对象太多

2. **CMS启动太晚**：`CMSInitiatingOccupancyFraction`设得太高，老年代快满了才开始GC，来不及清理

3. **内存碎片太多**：标记-清除算法产生碎片，大对象找不到连续空间

4. **业务流量突增**：突然大量对象晋升老年代，超过预期

## 解决办法

1. **调小CMS触发阈值**：降低`-XX:CMSInitiatingOccupancyFraction`，让CMS早点开始回收，预留更多空间

2. **加大老年代空间**：增加堆内存，调大老年代比例

3. **开启压缩**：`-XX:+UseCMSCompactAtFullCollection`，Full GC时做一次压缩消除碎片

4. **控制Full GC压缩频率**：`-XX:CMSFullGCsBeforeCompaction`，多少次Full GC后压缩一次

5. **升级收集器**：换G1或ZGC，没有concurrent mode failure问题
