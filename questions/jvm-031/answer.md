OOM（OutOfMemoryError）是JVM内存不够分配时抛出的错误，常见有以下7种场景：

## 1. 堆内存溢出（Java heap space）

- **原因**：堆中对象太多，GC后还是放不下新对象

- **典型场景**：内存泄漏、一次性加载大量数据、集合对象持有引用不释放

- **参数**：`-Xmx` 设置堆最大值

## 2. 元空间溢出（Metaspace）

- **原因**：加载的类元数据超过元空间上限

- **典型场景**：动态生成大量代理类、热部署频繁、应用数量多

- **参数**：`-XX:MaxMetaspaceSize`

## 3. 虚拟机栈/本地方法栈OOM

- **注意**：栈深度超限是StackOverflowError，不是OOM

- **OOM场景**：创建线程数量太多，每个线程分配的栈空间总和超出系统限制

- **典型场景**：线程池无限制创建线程

- **参数**：`-Xss` 每个线程栈大小（栈越小，能创建的线程越多）

## 4. 直接内存溢出（Direct buffer memory）

- **原因**：NIO的DirectByteBuffer分配的堆外内存超限

- **典型场景**：大量使用NIO、Netty框架，堆外内存未释放

- **参数**：`-XX:MaxDirectMemorySize`

## 5. GC overhead limit exceeded

- **特殊OOM**：GC花费了98%以上的时间但回收不到2%的内存

- 相当于GC一直在忙但没效果，JVM判定内存快耗尽了提前抛错

- 通常是堆内存泄漏的前兆

## 6. 数组分配溢出（Requested array size exceeds VM limit）

- 分配的数组大小超过了JVM允许的最大值（一般是Integer.MAX\_VALUE附近）

- 比较少见

## 7. 操作系统级OOM（Out of swap space / Kill process）

- JVM本身内存没超限，但操作系统物理内存+交换区耗尽

- 操作系统可能直接把JVM进程杀掉（Linux OOM Killer）
