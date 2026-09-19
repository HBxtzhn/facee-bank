## 追问 1：Young GC一定比Full GC快吗？

答：通常是这样，但不是绝对的。

Young GC只扫新生代，范围小，一般很快。但如果新生代很大（比如堆设了几十G，新生代占了十几G），而且存活对象很多，Young GC的STW也会很长。

反过来，CMS的并发Full GC大部分阶段和用户线程一起跑，停顿时间反而可能很短。当然CMS如果退化成Serial Old的Full GC，那肯定比Young GC慢得多。

一般来说：G1之前的收集器，Young GC < Old GC < Full GC；G1和ZGC这些低延迟收集器的停顿时间和回收范围关系不大，主要看设计目标。
