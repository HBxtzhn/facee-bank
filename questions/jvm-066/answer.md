JVM性能分析工具分为命令行工具和可视化工具两大类，覆盖GC、内存、线程、CPU等各个维度。

## 一、JDK自带命令行工具（最常用，线上排查必备）

**1. jps — 查看Java进程**

- 列出所有Java进程ID和主类名

- 常用：`jps -l` 显示完整类名，`jps -v` 显示JVM参数

- 排查第一步，先找到进程ID

**2. jstat — 实时监控GC统计**

- 查看GC次数、耗时、各区内存占用

- 常用：`jstat -gcutil pid 1000 10` 每秒输出一次GC概况，输出10次

- 最常用的线上GC监控工具，轻量不影响性能

**3. jmap — 内存分析**

- 生成堆转储快照（heap dump）：`jmap -dump:format=b,file=heap.hprof pid`

- 查看对象统计：`jmap -histo pid` 显示各类对象数量和大小

- 查看堆信息：`jmap -heap pid`

- 注意：dump的时候会触发Full GC，线上谨慎使用

**4. jstack — 线程分析**

- 打印线程栈快照：`jstack pid`

- 排查死锁、CPU飙高、线程阻塞的利器

- CPU高的时候：top找高CPU线程 → 转16进制 → jstack找对应线程栈

**5. jinfo — 查看JVM参数**

- 查看JVM的配置参数：`jinfo -flags pid`

- 查看某个参数的值：`jinfo -flag 参数名 pid`

- 还可以运行时修改部分参数

**6. jhat — 分析堆dump**

- 启动HTTP服务分析heap dump文件

- 功能比较基础，一般用MAT代替

## 二、可视化分析工具

**1. MAT（Memory Analyzer Tool）**

- Eclipse出品的堆内存分析神器

- 可以分析内存泄漏、查看大对象、 dominator tree

- 最常用的内存泄漏排查工具

**2. VisualVM**

- JDK自带的可视化监控工具（新版本需要单独装）

- 可以监控CPU、内存、线程、GC，还能做抽样分析

- 功能全面，开发调试常用

**3. JConsole**

- JDK自带的JMX监控工具

- 基础的内存、线程、类加载监控

- 功能简单，适合快速查看

**4. GC日志分析工具**

- **GCViewer**：本地工具，导入gc.log生成图表

- **GCEasy**：在线工具，上传GC日志自动分析出报告

- 分析GC调优效果必备

**5. Arthas（阿尔萨斯）**

- 阿里开源的Java诊断工具，非常强大

- 线上排查神器：方法耗时追踪、热更新代码、查看方法参数、监控JVM

- 不用重启应用，attach上去就能用

- 现在线上排查首选工具

## 三、操作系统级工具

- **top / htop**：看进程CPU、内存整体占用

- **vmstat**：系统整体内存、CPU、IO情况

- **pidstat**：查看进程的CPU、内存、IO详细统计

- **perf**：Linux性能分析工具，火焰图生成

- **strace**：跟踪系统调用
