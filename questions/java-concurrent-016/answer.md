`Future.get(timeout, unit)` 只限制调用线程等待结果的时间。抛出 `TimeoutException` 时，后台任务可能仍在运行。调用 `cancel(true)` 可以尝试中断执行线程，但中断是协作机制，任务不检查中断状态，或者底层调用不响应中断，任务仍可能继续执行。

```java
Future future = executor.submit(this::callRemoteService);

try {
    return future.get(200, TimeUnit.MILLISECONDS);
} catch (TimeoutException e) {
    future.cancel(true);
    throw new IllegalStateException("调用超时", e);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
    throw new IllegalStateException("等待任务时被中断", e);
} catch (ExecutionException e) {
    throw new IllegalStateException("任务执行失败", e.getCause());
}
```

这段代码还不够替代网络超时。HTTP、数据库和 Redis 客户端仍要配置连接、读取和总调用超时，否则线程可能一直卡在不响应中断的底层操作里。

任务代码收到 `InterruptedException` 后，一般要结束当前工作；如果无法在当前层结束，应恢复中断标记，让上层继续处理：

```java
try {
    blockingCall();
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
    return;
}
```

`CompletableFuture.orTimeout()` 会让 `CompletableFuture` 超时后以异常完成，但不会自动终止底层任务。`CompletableFuture.cancel(true)` 的参数在该实现中也不会触发线程中断。使用 `CompletableFuture` 编排阻塞任务时，仍要给底层调用配置超时和取消方案。
