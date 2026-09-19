G1的回收流程主要分为三种GC模式：**Young GC、Mixed GC、Full GC**，加上后台的并发标记周期。

## 一、Young GC（年轻代回收）

**触发条件**：Eden区满了，新对象分配不下

**执行流程：**

1. **STW暂停用户线程**

2. 扫描GC Roots + 扫描RSet，找出所有新生代存活对象

3. 把存活对象复制到空的Survivor Region

4. 清空所有Eden Region

5. 年龄达到阈值的对象晋升到老年代Region

6. **恢复用户线程**

**特点**：和传统Young GC类似，但Region是动态的，新生代大小不固定。

## 二、并发标记周期（为Mixed GC做准备）

**触发条件**：老年代占用达到IHOP阈值（默认45%）

**五个阶段：**

1. **初始标记（Initial Mark）**：STW，标记GC Roots直接引用的对象，这个阶段顺便搭在Young GC上完成

2. **并发标记（Concurrent Mark）**：和用户线程并发，从初始标记的对象开始遍历整个堆，标记所有存活对象

3. **最终标记（Remark）**：STW，处理SATB队列里的残留引用，完成最终标记

4. **筛选回收（Cleanup）**：STW，统计每个Region的存活对象比例和回收价值，排序，选出要回收的Region，清理空闲Region

## 三、Mixed GC（混合回收）

**触发条件**：并发标记完成后，老年代有足够多的可回收Region

**执行流程：**

1. **STW暂停用户线程**

2. 回收所有新生代Region + 选出来的高价值老年代Region

3. 把这些Region里的存活对象复制到空的Region中

4. 回收完成，清空被回收的Region

5. **恢复用户线程**

**特点**：

- 每次回收多少老年代Region由停顿预测模型决定

- 可以执行多次Mixed GC，逐步把垃圾多的老年代Region清完

- 这是G1的核心模式，用增量回收代替整代回收

## 四、Full GC（兜底）

**触发条件**：Mixed GC赶不上对象分配速度，老年代被填满，或者巨型对象分配失败

**执行**：退化为单线程Full GC（Java10之前），整堆回收+压缩，停顿很长。Java10以后G1的Full GC改成了多线程并行。

## G1整体流程图

```
对象分配 → Eden区满 → Young GC → 年龄达标晋升老年代
                              ↓
                    老年代达到IHOP阈值
                              ↓
              初始标记(STW) → 并发标记 → 最终标记(STW) → 筛选回收
                              ↓
                         Mixed GC × N次
                              ↓
                    老年代又满了 → 循环 / Full GC兜底
```
