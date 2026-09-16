对于线程池仍在运行、并且调用线程可以接受同步执行任务的场景，可以考虑 `CallerRunsPolicy`。它不能提供“任何情况下都不丢任务”的保证：线程池关闭后任务会被丢弃，进程崩溃也无法靠内存中的拒绝策略恢复任务；需要强保证时应配合持久化或消息队列。

这里我们再来结合 `CallerRunsPolicy` 的源码来看看：

```java
public static class CallerRunsPolicy implements RejectedExecutionHandler {

        public CallerRunsPolicy() { }

        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            //只要当前程序没有关闭，就用执行execute方法的线程执行该任务
            if (!e.isShutdown()) {

                r.run();
            }
        }
    }
```

从源码可以看出，只要当前程序不关闭就会使用执行 `execute` 方法的线程执行该任务。
