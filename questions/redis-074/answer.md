- **基本实现**：使用 Redis 的 `HSET`（Hash 结构）存储锁信息。key 为锁名，field 为线程标识（UUID + threadId），value 为重入次数。通过 Lua 脚本保证原子性。
- **加锁逻辑**：`HSETNX lock_key thread_id 1`，如果 key 不存在则设置成功（获取锁）；如果 key 存在且 field 是当前线程，则 value +1（可重入）；否则返回失败（锁被其他线程持有）。
- **解锁逻辑**：Lua 脚本检查 field 是否为当前线程，是则 value -1，减到 0 时 `DEL` 锁；不是则忽略。保证只有锁持有者能释放锁。
- **阻塞等待**：加锁失败时，通过 Redis 的 `PUBLISH/SUBSCRIBE` 机制监听锁释放事件，收到通知后重试加锁。
- **Watch Dog 续期**：默认锁过期时间 30s，每 10s（`lockWatchdogTimeout / 3`）自动续期到 30s，防止业务未完成锁就过期。

## 扩展知识

**Redisson 分布式锁的 Lua 脚本（加锁）**：
```lua
if (redis.call('exists', KEYS[1]) == 0) then
    redis.call('hset', KEYS[1], ARGV[2], 1);
    redis.call('pexpire', KEYS[1], ARGV[1]);
    return nil;
end;
if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then
    redis.call('hincrby', KEYS[1], ARGV[2], 1);
    redis.call('pexpire', KEYS[1], ARGV[1]);
    return nil;
end;
return redis.call('pttl', KEYS[1]);
```

**公平锁实现**：
- 使用额外的 `HSET` 存储等待队列（`lock_key:uuid:threadId`）
- 通过 `ZSET` 记录等待顺序（score 为时间戳）
- 释放锁时，从队列头部取下一个等待者，`PUBLISH` 通知其加锁

**Redisson 锁的局限性**：
1. **主从切换丢锁**：主节点加锁成功后宕机，锁数据未同步到从节点，新主节点无锁信息，其他线程可获取锁。解决方案：RedLock 算法（向 N 个独立节点加锁，半数以上成功才算获锁）。
2. **GC 停顿风险**：JVM GC 导致线程暂停，Watch Dog 未能及时续期，锁过期后被其他线程获取。
3. **性能开销**：每次加锁/解锁需执行 Lua 脚本 + 订阅发布，比本地锁慢。

## 对比表格

| 特性 | Redisson 普通锁 | Redisson 公平锁 | RedLock |
|------|---------------|----------------|---------|
| 数据结构 | Hash | Hash + ZSet | 多个独立 Redis 实例 |
| 获取顺序 | 非公平（竞争） | 按等待队列顺序 | 向 N 个节点尝试 |
| 可重入 | 支持（hincrby） | 支持 | 支持 |
| 续期机制 | Watch Dog | Watch Dog | 无（固定过期时间） |
| 主从安全 | 否（可能丢锁） | 否 | 是（多数派确认） |
| 性能 | 较高 | 较低（队列维护开销） | 低（N 次网络请求） |
| 适用场景 | 一般业务 | 需要公平调度 | 高一致性要求 |
