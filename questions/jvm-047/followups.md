## 追问 1：ZGC有Young GC和Full GC的概念吗？

答：ZGC是不分代的（Java17之前），所以没有传统意义上的Young GC和Full GC之分。

ZGC每次都是全局并发标记+并发整理整个堆，停顿时间极短（亚毫秒级）。虽然回收的是整堆，但因为几乎全程并发，STW很短，所以不会有传统Full GC那种长时间卡顿的问题。

Java21开始ZGC也有分代版本了（Generational ZGC），分为年轻代和老年代，就有Young GC和Old GC的概念了，进一步降低停顿和提升吞吐量。
