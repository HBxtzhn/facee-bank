## 追问 1：G1也有并发失败吗？失败了也退化成单线程吗？

答：G1也有类似的并发失败，叫**Evacuation Failure（转移失败）**，就是Mixed GC的时候，Survivor区或者目标Region放不下复制过来的存活对象了。

但G1不会退化成单线程。G1的处理方式是：暂停用户线程，用GC线程继续完成剩下对象的转移，也是STW，但还是多GC线程并行做，比CMS退化成单线程Serial Old要好很多。

而且G1有预测机制，会尽量选能回收完的Region，减少Evacuation Failure的概率。这也是G1比CMS先进的地方之一。
