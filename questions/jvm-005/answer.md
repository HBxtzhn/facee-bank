先分清上涨的是 Java 堆、进程 RSS 还是容器总内存。`-Xmx` 只限制 Java 堆，进程还会使用 Metaspace、Code Cache、线程栈、直接内存、GC 自身数据结构和本地库内存。

先查看 JVM 和系统两边的数据：

```bash
jcmd  GC.heap_info
jcmd  VM.flags
jcmd  VM.native_memory summary
```

`VM.native_memory` 依赖 Native Memory Tracking（NMT），需要在 JVM 启动时开启，例如 `-XX:NativeMemoryTracking=summary`。它会带来额外开销，不能在故障发生后临时补开。

NMT 主要统计 JVM/HotSpot 自身管理的本地内存，无法覆盖 JNI 或第三方本地库的全部内存分配。RSS 持续上涨而 NMT 没有对应变化时，还要借助操作系统和本地内存分析工具继续排查。

常见 OOM 信息对应不同方向：

| OOM 信息                         | 常见排查方向                                           |
| -------------------------------- | ------------------------------------------------------ |
| `Java heap space`                | 大对象、对象持有、无界集合、缓存、一次查询返回过多数据 |
| `GC overhead limit exceeded`     | 堆接近耗尽，GC 花费大量时间但回收很少                  |
| `Metaspace`                      | 动态类生成、类加载器泄漏、Metaspace 上限过小           |
| `Direct buffer memory`           | NIO 直接内存、Netty Buffer、释放延迟或上限不合理       |
| `unable to create native thread` | 线程数过多、进程限制、容器内存不足、单线程栈过大       |

Heap Dump 可以使用 MAT、VisualVM 等工具分析。先看占用最大的对象、Dominator Tree、到 GC Roots 的引用链和可疑类加载器，不要只根据对象数量下结论。

需要手动转储时，可以使用：

```bash
jcmd  GC.heap_dump filename=/path/with/enough/space/heap.hprof
```

这个命令可能对应用产生明显影响。执行前应评估堆大小、磁盘余量、I/O 和停顿风险。生产实例仍在承载流量时，优先在摘流量后的异常实例操作。`jmap -dump:live` 可能触发 Full GC，不适合当作无风险命令直接执行。
