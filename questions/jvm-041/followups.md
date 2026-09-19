## 追问 1：Parallel Scavenge和ParNew都是并行新生代收集器，有什么区别？

答：表面看都是多线程复制算法，但设计目标完全不同：

ParNew的关注点是**降低用户线程停顿时间**，是为了和CMS搭配设计的，是CMS的专属新生代搭档。

Parallel Scavenge的关注点是**吞吐量**（用户代码时间/总时间），目标是让CPU尽可能多地跑业务代码，适合后台批量计算、数据分析这种场景。

另外Parallel Scavenge有自适应调节策略（-XX:+UseAdaptiveSizePolicy），JVM自动调整Eden、Survivor比例等参数，开发者只需要设最大堆和目标吞吐量就行。
