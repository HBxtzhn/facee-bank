JIT编译后的本地机器码存放在 **Code Cache（代码缓存）** 区域。

## Code Cache的位置和性质

1. **属于堆外内存**：Code Cache是JVM的一块非堆内存，不在堆里，也不属于元空间

2. **本地内存分配**：直接在操作系统本地内存中分配

3. **有大小上限**：不是无限增长的，有固定大小限制

## Code Cache的核心参数

|参数|默认值|作用|
|---|---|---|
|`-XX:InitialCodeCacheSize`|约2.4MB|Code Cache初始大小|
|`-XX:ReservedCodeCacheSize`|C2模式约240MB|Code Cache最大保留大小|
|`-XX:+UseCodeCacheFlushing`|默认开启|Code Cache满了之后刷新回收旧代码|

## Code Cache满了会怎样？

1. JIT编译器停止编译新代码，所有新代码退回解释执行，性能骤降

2. 日志中会出现 `CodeCache is full. Compiler has been disabled.` 警告

3. Java7及之前Code Cache满了就彻底不编译了；Java8+开启CodeCache刷新后会回收不常用的编译代码

## Code Cache里存了什么？

不仅是JIT编译后的代码，还包括：

- JNI的native方法绑定代码

- 解释器的stub代码

- 各种运行时生成的适配器代码
