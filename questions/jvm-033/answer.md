**直接内存（Direct Memory）**也叫堆外内存，是不在JVM堆内管理、直接向操作系统申请的本地内存。

## 核心特点

**1. 不属于JVM运行时数据区规范**

- 直接内存不是《Java虚拟机规范》定义的内存区域

- 但Java NIO大量使用，实际开发中非常常见

- 不受 `-Xmx` 堆参数限制

**2. 典型使用方式：DirectByteBuffer**

- Java NIO通过 `ByteBuffer.allocateDirect()` 分配直接内存

- 返回的DirectByteBuffer对象在堆里，但它持有的真实数据缓冲区在堆外

- DirectByteBuffer对象本身很小，只是一个引用句柄

**3. 性能优势：零拷贝**

- 传统IO：数据从内核缓冲区 → JVM堆内存 → 用户使用，需要两次拷贝

- 直接内存：Java直接操作内核缓冲区的数据，少了一次拷贝

- 适合IO密集型场景（Netty、Kafka、文件传输等）

## 直接内存的回收

- 堆内的DirectByteBuffer对象被GC回收时，会触发Cleaner回调释放堆外内存

- 也可以手动调用 `cleaner.clean()` 主动释放

- 如果堆内DirectByteBuffer一直存活，堆外内存就一直占着，可能导致物理内存泄漏

## 常见参数

|参数|作用|
|---|---|
|`-XX:MaxDirectMemorySize`|限制最大直接内存大小，默认和堆的-Xmx差不多|
|`-XX:+DisableExplicitGC`|禁止System.gc()，可能影响直接内存回收|
