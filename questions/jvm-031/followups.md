## 追问 1：StackOverflowError和OutOfMemoryError有什么区别？

答：完全是两回事：

- StackOverflowError是**单个线程的栈深度超限**，比如递归没出口，方法调用层级太深，栈帧堆满了。这是线程栈内部的问题，不影响其他线程。

- OutOfMemoryError是**整体内存不够用了**，比如堆满了、元空间满了、创建不了新线程了。这是全局级别的内存问题。

简单说：StackOverflow是栈太深了，OOM是内存总量不够了。
