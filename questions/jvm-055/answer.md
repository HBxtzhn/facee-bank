CMS和G1在回收范围上有本质区别，核心是**传统分代 vs Region化分代**的架构差异。

## CMS的回收范围

**CMS是传统分代模型，回收范围是固定的：**

1. **Young GC**：只回收新生代（Eden + S0/S1），由ParNew负责

2. **Old GC（并发回收）**：只回收老年代，由CMS负责

3. **Full GC**：整个堆（新生代+老年代），退化成Serial Old时触发

CMS的老年代是一整块连续内存，每次Old GC都要回收整个老年代，不能选一部分回收。

## G1的回收范围

**G1是Region化模型，回收范围灵活可变：**

1. **Young GC**：回收所有新生代Region（Eden + Survivor）

    - 新生代Region数量不固定，可以动态调整

2. **Mixed GC（混合GC）**：回收所有新生代Region + 部分回收价值高的老年代Region

    - 这是G1的特色，不是回收全部老年代，只选垃圾最多的几个Region

    - 每次回收的Region数量可预测，从而控制停顿时间

3. **Full GC**：整个堆所有Region

    - G1也有Full GC，但尽量避免，G1的Full GC是单线程的（早期版本），很慢

## 核心区别总结

|维度|CMS|G1|
|---|---|---|
|内存布局|连续内存的分代模型|离散Region的分代模型|
|老年代回收|每次回收整个老年代|只选回收价值高的部分Region|
|停顿控制|不可预测，取决于存活对象多少|可预测，通过选Region数量控制|
|特有GC类型|Concurrent Mark Sweep|Mixed GC|
|回收粒度|整代回收|Region级回收|
