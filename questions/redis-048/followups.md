## 追问 1：为什么解锁要用 Lua 脚本？直接用 GET + DEL 不行吗？

**标准答案**：不行。GET 和 DEL 是两条独立命令，在 GET 判断锁属于自己之后、DEL 之前，锁可能过期被其他客户端获取，此时 DEL 会误删别人的锁。Lua 脚本在 Redis 中原子执行，不会被其他命令插入，保证判断和删除的原子性。

## 追问 2：如果锁的过期时间到了但业务还没执行完怎么办？

**标准答案**：使用 Redisson 的 Watch Dog 机制，它会在获取锁后启动定时任务，每隔 `lockWatchdogTimeout / 3`（默认 10 秒）检查锁是否仍被持有，如果是则续期到 `lockWatchdogTimeout`（默认 30 秒）。业务完成后自动取消续期。也可以手动实现类似机制。

## 追问 3：`SETNX` + `EXPIRE` 为什么不是原子操作？

**标准答案**：`SETNX` 和 `EXPIRE` 是两条独立命令。如果 `SETNX` 成功后进程崩溃，`EXPIRE` 未执行，锁将永不过期导致死锁。`SET key value NX PX timeout` 是单条命令，在 Redis 中原子执行，避免了这个问题。
