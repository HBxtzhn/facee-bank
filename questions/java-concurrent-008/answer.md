### 不要重复创建线程池

线程池用于复用线程，不应在每个请求或每次方法调用中重新创建。频繁创建会增加线程启动和销毁开销，也容易遗漏关闭逻辑。应用级线程池通常交给容器统一管理生命周期。

Spring 的 `@Async`、定时任务、Web 容器和部分客户端内部也会使用线程池。项目没有显式调用 `new ThreadPoolExecutor()`，不代表不存在需要配置和监控的线程池。

### 不要让长任务占满共享线程池

长时间阻塞的任务会占用工作线程，后续短任务只能排队。报表导出、文件处理和慢外部接口适合使用独立的执行资源，或者改成异步任务并向用户返回任务状态。

`CompletableFuture` 只负责任务编排，不会把阻塞式网络请求变成非阻塞操作。没有显式指定 `Executor` 的异步方法通常使用公共线程池，公共线程池被阻塞后，还可能影响应用中的其他异步任务。

### 清理线程上下文

线程池会复用线程。前一个任务写入 `ThreadLocal` 后没有清理，后续任务可能在同一线程上读到旧值，还可能让大对象长期被工作线程引用。

上下文传递和清理应由统一的任务包装器处理，在 `finally` 中恢复或删除原值。日志 MDC、登录信息和租户信息都要考虑这个问题。跨线程传递场景也可以使用 TransmittableThreadLocal，但仍要确认包装方式和清理时机。

### 正确关闭线程池

`shutdown()` 不再接收新任务，会继续处理已经提交的任务；`shutdownNow()` 会尝试中断正在执行的任务，并返回尚未开始执行的任务。两者都不会等待线程池彻底终止。

```java
executor.shutdown();
try {
    if (!executor.awaitTermination(30, TimeUnit.SECONDS)) {
        executor.shutdownNow();
        if (!executor.awaitTermination(30, TimeUnit.SECONDS)) {
            System.err.println("线程池未能正常退出");
        }
    }
} catch (InterruptedException e) {
    executor.shutdownNow();
    Thread.currentThread().interrupt();
}
```

任务需要正确响应中断，否则 `shutdownNow()` 也不能保证立即停止。应用关闭期间还要决定队列中的任务能否丢失；不能丢的业务任务不应只存在进程内存里。
