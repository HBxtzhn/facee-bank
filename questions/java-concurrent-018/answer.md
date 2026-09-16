在 Java 中，`volatile` 关键字可以保证变量的可见性。对某个 `volatile` 变量的写入 happens-before 于后续对同一变量的读取，因此读线程能够看到该写入以及写入前按 happens-before 传递过来的结果。这是 JMM 规定的语义，不等同于要求每次访问都绕过 CPU 缓存、直接读写物理主存。

`volatile` 关键字并非 Java 语言特有，但不同语言中的语义并不相同。Java 的 `volatile` 由 JMM 定义可见性和有序性保证，不能解释为“禁用 CPU 缓存”。

`volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保证。
