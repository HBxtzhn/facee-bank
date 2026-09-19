- **缓存**：最核心的场景。将热点数据缓存到 Redis，减少数据库压力，提升响应速度。常见模式：Cache-Aside（旁路缓存）、Read/Write Through、Write Behind。
- **分布式锁**：利用 `SET key value NX EX` 实现互斥访问，配合 Redlock 算法解决多节点下的锁可靠性问题。
- **计数器/限流**：利用 `INCR`/`DECR` 原子操作实现访问计数、接口限流（如滑动窗口限流）。
- **排行榜**：利用 ZSet（有序集合）实现实时排行榜，如游戏积分排名、热文排行。
- **消息队列**：利用 List 的 `LPUSH`/`BRPOP` 或 Stream 实现轻量级消息队列。
- **会话共享**：分布式系统中将 session 存储到 Redis，实现多节点间的会话共享。

## 扩展知识

- **缓存穿透/击穿/雪崩**：
  - 穿透：查询不存在的数据，用布隆过滤器或缓存空值解决。
  - 击穿：热点 key 过期瞬间大量请求打到 DB，用互斥锁或永不过期解决。
  - 雪崩：大量 key 同时过期，用随机过期时间或多级缓存解决。
- **双写一致性**：先更新 DB 再删除缓存（延迟双删策略可进一步保证一致性），或使用 Canal 监听 binlog 异步更新缓存。
- **Redis Stream**：Redis 5.0 引入的流数据结构，支持消费者组（Consumer Group），功能类似 Kafka，适合消息队列场景。

## 对比表格

| 场景 | 使用数据结构 | 关键命令/特性 | 注意事项 |
|------|------------|-------------|---------|
| 缓存 | String / Hash | GET/SET, EXPIRE | 缓存一致性、穿透/击穿/雪崩 |
| 分布式锁 | String | SET NX EX | 锁超时、Redlock、看门狗续期 |
| 排行榜 | ZSet | ZADD, ZRANGEBYSCORE | 数据量过大时注意内存占用 |
| 计数器 | String / Hash | INCR, DECR | 原子性保证 |
| 消息队列 | List / Stream | LPUSH/BRPOP, XADD/XREAD | Stream 支持消费者组 |
| 会话共享 | String / Hash | SET, EXPIRE | 设置合理过期时间 |
