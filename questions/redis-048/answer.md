1. **核心概念**：利用 Redis 的原子操作实现跨多个进程的互斥锁，确保同一时刻只有一个客户端能持有锁。
2. **关键细节拆解**：
   - **基本实现**：
     - **加锁**：`SET lock_key unique_value NX PX 30000`（NX 保证不存在时才设置，PX 设置过期时间防止死锁）
     - **解锁**：使用 Lua 脚本保证"判断 + 删除"的原子性
     ```
     if redis.call("get", KEYS[1]) == ARGV[1] then
         return redis.call("del", KEYS[1])
     else
         return 0
     end
     ```
   - **关键要素**：
     - **互斥性**：`NX` 保证只有一个客户端能成功设置 key
     - **防死锁**：设置过期时间（PX），锁到期自动释放
     - **防误删**：value 使用唯一标识（如 UUID + 线程ID），解锁时校验
     - **原子解锁**：Lua 脚本保证 GET+DEL 的原子性
   - **可重入锁**：使用 Hash 结构，key 为锁名，field 为客户端标识，value 为重入次数

## 扩展知识

- **Redisson 实现**：Redisson 提供了完善的分布式锁实现，支持可重入锁、公平锁、联锁（MultiLock）、红锁（RedLock）等。底层使用 Hash 结构存储锁信息，通过 Lua 脚本保证原子性，通过 Pub/Sub 实现锁释放通知。
- **锁续期（Watch Dog）**：Redisson 的 Watch Dog 机制，在锁即将过期前自动续期（默认每 10 秒检查一次，续期到 30 秒），防止业务未完成锁就过期。
- **公平锁**：使用有序集合（ZSet）存储等待队列，按请求顺序分配锁，通过 `BLPOP` 或 Pub/Sub 通知下一个等待者。
- **`SET` 命令的原子性**：`SET key value NX PX timeout` 是原子操作，等同于 `SETNX` + `EXPIRE` 但避免了两者之间的竞态条件。

## 对比表格

| 实现方式 | 原子性 | 可重入 | 防误删 | 锁续期 | 适用场景 |
|---------|--------|--------|--------|--------|---------|
| `SETNX` + `EXPIRE` | ❌（两条命令） | ❌ | ❌ | ❌ | 不推荐 |
| `SET NX PX` | ✅ | ❌ | ✅（需校验 value） | ❌ | 简单场景 |
| `SET NX PX` + Lua 解锁 | ✅ | ❌ | ✅ | ❌ | 一般场景 |
| Redisson WatchDog | ✅ | ✅ | ✅ | ✅ | 生产推荐 |
| RedLock | ✅ | ✅ | ✅ | ✅ | 高可用要求 |
