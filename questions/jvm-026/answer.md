JVM运行时数据区按**线程共享/线程私有**分为两大类，共5个核心区域 + 1个特殊区域：

## 线程私有区域（随线程创建而创建，线程销毁而销毁）

**1. 程序计数器（Program Counter Register）**

- 记录当前线程执行的字节码指令地址（行号）

- 线程切换后恢复执行位置的依据

- 唯一一个JVM规范中不会OOM的区域

**2. 虚拟机栈（VM Stack）**

- 每个方法执行时都会创建一个栈帧（Stack Frame）入栈

- 栈帧包含：局部变量表、操作数栈、动态链接、方法出口

- 栈深度超出限制抛StackOverflowError；栈扩展失败抛OutOfMemoryError

- 常见参数：`-Xss` 设置每个线程栈大小

**3. 本地方法栈（Native Method Stack）**

- 为Native本地方法服务，结构和虚拟机栈类似

- HotSpot虚拟机直接把本地方法栈和虚拟机栈合二为一

- 同样会抛出StackOverflowError和OOM

## 线程共享区域（JVM启动时创建，所有线程共享）

**4. 堆（Heap）**

- JVM内存最大的一块，所有对象实例和数组的分配区域

- GC的主要工作区域，分为新生代（Eden + S0 + S1）和老年代

- 内存不足时抛`java.lang.OutOfMemoryError: Java heap space`

- 常见参数：`-Xms` 初始堆大小、`-Xmx` 最大堆大小

**5. 方法区（Method Area）**

- 存储已加载的类信息、常量、静态变量、JIT编译后的代码等

- Java7及之前叫永久代（PermGen），Java8之后改为元空间（Metaspace），使用本地内存实现

- 内存不足时抛元空间OOM

## 特殊区域

**6. 直接内存（Direct Memory）**

- 不属于JVM运行时数据区规范定义的部分

- NIO通过DirectByteBuffer直接操作的堆外内存

- 不受-Xmx限制，但受操作系统总内存限制，也会OOM

## 内存区域对比表

|区域|线程归属|存储内容|OOM风险|常见异常|
|---|---|---|---|---|
|程序计数器|私有|字节码行号|无|无|
|虚拟机栈|私有|方法栈帧|有|StackOverflowError / OOM|
|本地方法栈|私有|Native方法栈帧|有|StackOverflowError / OOM|
|堆|共享|对象实例、数组|有|Java heap space|
|方法区/元空间|共享|类信息、常量、静态变量|有|Metaspace|
|直接内存|共享|堆外NIO缓冲区|有|Direct buffer memory|
