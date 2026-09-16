# 常见垃圾回收器怎么选？（含 G1 调优）

请对比 Serial、Parallel、CMS、G1、ZGC 的适用场景。

![GC 时间线](./assets/gc-timeline.png)

线上服务（8C16G、堆 8G、要求 P99 稳定）你会怎么选？为什么？
