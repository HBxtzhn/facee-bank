## 追问 1：Redisson 分布式锁如何解决可重入问题？

使用 Hash 结构存储锁：key 为锁名，field 为线程唯一标识（`UUID:threadId`），value 为重入次数。加锁时，如果 field 已存在且是当前线程，则 `HINCRBY` 将 value +1；解锁时 `HINCRBY -1`，减到 0 时 `DEL` 整个 key。这样同一线程可多次获取同一把锁，且必须释放相同次数才算真正释放。

## 追问 2：主从切换导致丢锁的问题怎么解决？

Redisson 提供 RedLock 算法：部署 N 个独立的 Redis 主节点（无从节点）。加锁时依次向所有节点尝试加锁，超过半数（N/2+1）成功且总耗时小于锁过期时间，才算获取锁成功。失败时向所有节点发送解锁请求。但 RedLock 有争议（Martin Kleppmann 指出时钟跳跃等问题），生产环境建议：① 使用 ZooKeeper 等强一致中间件实现分布式锁；② 业务层做幂等和补偿。

## 追问 3：Watch Dog 续期失败会怎样？

Watch Dog 通过 `TimerTask` 每 `lockWatchdogTimeout / 3`（默认 10s）执行一次续期 Lua 脚本。续期失败的场景：① 网络分区，客户端与 Redis 断开 → 锁过期后被其他线程获取，原线程继续执行可能导致并发问题；② JVM GC 停顿超过锁过期时间 → Watch Dog 未能及时续期。解决方案：合理设置 `lockWatchdogTimeout`（业务最大执行时间），监控 GC 日志，网络异常时主动释放锁。
