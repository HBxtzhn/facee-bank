## 追问 1：Concurrent Mode Failure和Promotion Failure有什么区别？

答：都是GC失败，但阶段和原因不一样：

Promotion Failure（晋升失败）发生在Young GC阶段：Young GC后存活对象要晋升老年代，老年代空间不够，晋升失败。一般晋升失败后会触发Full GC。

Concurrent Mode Failure发生在CMS并发回收阶段：CMS正在和用户线程并发跑的时候，老年代突然不够用了，并发回收失败，退化成Serial Old Full GC。

简单说：晋升失败是Young GC时晋升失败；并发模式失败是CMS并发回收过程中失败。两者最终都可能触发Full GC，但触发的时机不同。
