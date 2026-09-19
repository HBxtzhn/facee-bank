## 追问 1：ThreadLocal为什么用弱引用？用强引用会有什么问题？

答：ThreadLocal的key（ThreadLocal对象本身）是弱引用，存在ThreadLocalMap里。

如果用强引用：当外部的ThreadLocal引用没了，但线程还活着（比如线程池的线程长期存活），ThreadLocalMap里的key强引用着ThreadLocal对象，导致它永远回收不了，value也跟着泄漏。

用弱引用的话：外部ThreadLocal引用消失后，下次GC就把key回收了，key变成null。后续ThreadLocal调用set/get/remove的时候会清理这些key为null的entry，释放value。

但注意：如果线程一直不调用ThreadLocal的这些方法，value还是会泄漏，所以用完ThreadLocal一定要手动remove。
