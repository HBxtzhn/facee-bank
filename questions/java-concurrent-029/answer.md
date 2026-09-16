JDK 已经为虚拟线程补了不少观测能力。

### 使用 `jcmd` 导出线程转储

传统 `jstack` 面对成千上万个虚拟线程时不太合适。JDK 提供了新的线程转储能力：

```bash
jcmd  Thread.dump_to_file -format=json thread-dump.json
```

也可以导出文本格式：

```bash
jcmd  Thread.dump_to_file -format=text thread-dump.txt
```

JSON 格式更适合工具分析，尤其是虚拟线程数量很多时。

### 使用 JFR 观察虚拟线程事件

JFR 中和虚拟线程相关的事件包括：

- `jdk.VirtualThreadStart`
- `jdk.VirtualThreadEnd`
- `jdk.VirtualThreadPinned`
- `jdk.VirtualThreadSubmitFailed`

其中 `jdk.VirtualThreadPinned` 对排查 Pinning 很有用。JDK 24 以后，`synchronized` 相关 Pinning 大多被解决，但 native/FFM 等剩余场景仍然可以通过 JFR 观察。

### 临时打开 Pinning 栈追踪

在 JDK 21 到 JDK 23 中，可以临时使用：

```bash
-Djdk.tracePinnedThreads=full
```

它会在虚拟线程阻塞且被固定时打印调用栈，适合本地或测试环境定位问题。JDK 24 的 JEP 491 之后，`synchronized` 相关的主要 Pinning 场景已经改进；native/FFM 等剩余边界仍然建议结合 JFR 和线程转储判断。
