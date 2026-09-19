Young GC（Minor GC）是针对新生代的垃圾回收，触发条件主要有以下几种：

## 1. Eden区空间不足（最主要）

- **触发场景**：新对象分配时，Eden区剩余空间放不下这个对象

- **这是Young GC最常见、最主要的触发原因**

- 程序不断new对象，Eden逐渐被填满，满了就触发一次Young GC

- Young GC回收Eden和一块Survivor的垃圾，存活对象复制到另一块Survivor

## 2. 大对象分配触发

- 超过`-XX:PretenureSizeThreshold`的大对象直接进老年代

- 但如果大对象老年代也放不下，可能先触发一次Young GC看看能不能腾出空间

- 大对象本身也可能因为Eden放不下而提前触发Young GC

## 3. 主动触发

- 代码里调用`System.gc()`，一般会触发Full GC，但也可能伴随Young GC

- 某些JVM工具、监控命令也可能主动触发

## 4. 分配担保检查前触发

- Young GC前会检查老年代空间够不够晋升担保

- 某些情况下担保检查不通过会直接转Full GC，Full GC之前也会先做Young GC

## Young GC执行后的结果

1. **正常情况**：Eden清空，存活对象复制到Survivor，年龄+1

2. **Survivor装不下**：存活对象通过分配担保直接晋升老年代

3. **年龄达标**：达到晋升年龄阈值的对象晋升老年代
