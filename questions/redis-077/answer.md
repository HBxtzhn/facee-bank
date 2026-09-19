- **定义**：Watch Dog 是 Redisson 分布式锁的自动续期机制。当客户端获取锁后，如果未指定锁的过期时间（`leaseTime`），Redisson 会启动一个后台定时任务，每隔 `lockWatchdogTimeout / 3`（默认 10s）自动将锁的过期时间重置为 `lockWatchdogTimeout`（默认 30s）。
- **目的**：防止业务执行时间超过锁的过期时间，导致锁提前释放、其他线程获取锁，造成并发问题。
- **触发条件**：仅在 `tryLock()` 或 `lock()` 不指定 `leaseTime` 时启用。如果指定了 `leaseTime`，Watch Dog 不生效。
- **停止条件**：① 主动调用 `unlock()` 释放锁；② 客户端宕机（Watch Dog 线程停止，锁到期后自动释放）；③ 锁被其他线程强制释放（不推荐）。

## 扩展知识

**Watch Dog 的实现原理**：
```java
// RedissonLock 中的核心逻辑
public void lock(long leaseTime, TimeUnit unit) {
    // ...
    if (leaseTime == -1) {
        // 不指定 leaseTime，启用 Watch Dog
        tryLockWaiter(); // 内部启动定时续期
    }
}

// 续期 Lua 脚本
private void renewExpiration() {
    ExpirationEntry ee = EXPIRATION_RENEWAL_MAP.get(getEntryName());
    Timeout task = commandExecutor.getConnectionManager().newTimeout(new TimerTask() {
        @Override
        public void run(Timeout timeout) throws Exception {
            // 执行续期 Lua 脚本
            Boolean result = commandExecutor.evalWriteAsync(...,
                "if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then " +
                "    redis.call('pexpire', KEYS[1], ARGV[1]); " +
                "    return 1; " +
                "end; " +
                "return 0;", ...);
            if (result) {
                // 续期成功，继续调度下一次续期
                renewExpiration();
            }
        }
    }, internalLockLeaseTime / 3, TimeUnit.MILLISECONDS);
}
```

**Watch Dog 的局限性**：
1. **JVM GC 停顿**：长时间 Full GC 导致 Watch Dog 线程无法执行续期，锁过期。解决方案：监控 GC 日志，调整 JVM 参数减少 GC 停顿。
2. **网络分区**：客户端与 Redis 网络断开，续期请求无法到达 Redis。解决方案：合理设置 `lockWatchdogTimeout`，网络恢复后检查锁状态。
3. **单机风险**：Watch Dog 运行在客户端进程，进程崩溃则续期停止。这是预期行为（防止死锁），但需确保业务有重试和补偿机制。

**配置参数**：
- `lockWatchdogTimeout`：锁的默认过期时间（默认 30000ms），可通过 `Config.setLockWatchdogTimeout()` 修改
- 续期间隔 = `lockWatchdogTimeout / 3`（默认 10s）
- 建议设置：`lockWatchdogTimeout` = 业务最大执行时间 * 2（留足余量）

## 对比表格

| 场景 | Watch Dog 行为 | 锁状态 |
|------|---------------|--------|
| 正常执行 + 主动释放 | 续期正常，unlock 后停止 | 正常释放 |
| 业务执行时间 > 30s | 自动续期，锁不过期 | 安全持有 |
| 客户端宕机 | 续期停止，30s 后锁自动释放 | 自动释放（防死锁） |
| JVM Full GC > 30s | 续期延迟，锁可能过期 | 可能丢失 |
| 网络分区 > 30s | 续期失败，锁过期 | 可能丢失 |
| 指定 leaseTime | Watch Dog 不启用 | 到期自动释放 |
