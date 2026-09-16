# JVM 运行时数据区包含哪些部分？

请说明 JVM 运行时数据区的划分，并指出哪些区域是线程私有的、哪些是线程共享的。

![JVM 内存结构](./assets/memory-layout.png)

**要求**（这一题同时用于验证 Markdown 全元素渲染）：

1. 列出*线程私有*区域；
2. 列出*线程共享*区域；
3. 说明 **OutOfMemoryError** 与 `StackOverflowError` 分别可能出现在哪里。

> 提示：区分「规范定义」与「具体实现」。

---

参考表：

| 区域 | 归属 | 主要异常 |
|---|---|---|
| 程序计数器 | 线程私有 | 无 |
| 虚拟机栈 | 线程私有 | StackOverflowError / OOM |
| 本地方法栈 | 线程私有 | StackOverflowError / OOM |
| 堆 | 线程共享 | OutOfMemoryError |
| 方法区（元空间） | 线程共享 | OutOfMemoryError |

延伸阅读：[JVM 规范（JVMS）第 2 章](https://docs.oracle.com/javase/specs/jvms/se17/html/jvms-2.html)
