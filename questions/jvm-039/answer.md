**逃逸分析（Escape Analysis）**是JIT编译器的一项优化技术，分析对象的作用域范围，判断对象是否会"逃逸"出方法外部，从而决定要不要做栈上分配等优化。

## 什么是逃逸？

一个对象在方法内被创建后，如果：

- **不逃逸**：对象只在方法内部使用，外部引用不到

- **逃逸**：对象被方法外部引用到了（比如作为返回值返回、赋值给外部变量、传入其他线程）

```java
// 不逃逸：sb只在方法内部用，外部拿不到
public void noEscape() {
    StringBuffer sb = new StringBuffer();
    sb.append("hello");
}

// 逃逸：sb作为返回值出去了，外部能拿到
public StringBuffer escape() {
    StringBuffer sb = new StringBuffer();
    sb.append("hello");
    return sb;
}
```

## 逃逸分析能触发的三大优化

**1. 栈上分配（Stack Allocation）**

- 对象不逃逸的话，直接在栈上分配内存

- 方法结束栈帧弹出，对象自动销毁，不需要GC回收

- 减轻GC压力，特别适合大量小对象的场景

**2. 标量替换（Scalar Replacement）**

- 比栈上分配更激进：把对象拆解成它的成员字段（标量）

- 字段直接存在栈上的局部变量表里，连对象头都省了

- 性能收益最大的优化

**3. 同步消除（Lock Elision）**

- 如果判断锁对象只在当前线程可见，其他线程访问不到

- 就把synchronized同步锁消除掉，因为根本不存在竞争

- 比如StringBuffer的方法都是synchronized的，但局部变量的StringBuffer锁会被消除

## 开启参数

- `-XX:+DoEscapeAnalysis`：开启逃逸分析（Java8默认开启）

- `-XX:+EliminateAllocations`：开启标量替换

- `-XX:+EliminateLocks`：开启同步消除
