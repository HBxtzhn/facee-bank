**TLAB（Thread Local Allocation Buffer，线程本地分配缓冲区）**是JVM在堆的Eden区为每个线程分配的一小块私有内存，用于优化对象分配效率。

## 核心工作机制

1. **私有分配，无锁竞争**

    - 每个线程在Eden区有一块专属的TLAB

    - 线程创建小对象时，直接在自己的TLAB里分配，不需要加锁

    - 避免了多线程同时在Eden分配内存的CAS竞争开销

2. **TLAB满了怎么办**

    - 当前TLAB空间不够分配新对象时，线程会向Eden申请一块新的TLAB

    - 原来的TLAB里的存活对象会在下次Young GC时被复制走

    - 太大的对象放不下TLAB，直接在Eden公共区分配

3. **空间浪费的折中**

    - TLAB尾部可能有一小块空间用不完就废弃了，叫"浪费空间"

    - JVM通过`-XX:TLABWasteTargetPercent`控制浪费比例（默认1%）

    - 是空间换时间的典型优化

## 关键参数

|参数|作用|
|---|---|
|`-XX:+UseTLAB`|开启TLAB（默认开启）|
|`-XX:TLABSize`|手动设置TLAB大小|
|`-XX:+ResizeTLAB`|自适应调整TLAB大小（默认开启）|
|`-XX:TLABWasteTargetPercent`|TLAB浪费空间占Eden比例|

## 扩展知识：为什么需要TLAB？

对象分配在堆上，堆是线程共享的。如果没有TLAB，多个线程同时new对象分配内存时，需要通过CAS加锁来保证分配地址的唯一性，高并发下锁竞争会成为性能瓶颈。

TLAB让每个线程先在自己的私有缓冲区分配，只有缓冲区用完了才去竞争全局内存，大大减少了锁竞争次数，是JVM优化对象分配速度的重要手段。
