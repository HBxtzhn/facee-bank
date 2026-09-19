- **String**：最基本的类型，可存储字符串、整数、浮点数。常用于缓存、计数器、分布式锁。
- **Hash**：键值对集合，适合存储对象。内部编码为 `ziplist`（小对象）或 `hashtable`。
- **List**：有序列表，支持头尾操作。底层为 `quicklist`（ziplist + 双向链表）。用于消息队列、时间线。
- **Set**：无序不重复集合。底层为 `intset`（纯整数）或 `hashtable`。用于标签、交并集运算。
- **ZSet（有序集合）**：每个元素关联一个 score，按 score 排序。底层为 `ziplist`（小对象）或 `skiplist + hashtable`。用于排行榜、延迟队列。
- **高级类型**（Redis 5.0+）：
  - **Stream**：支持消费者组的消息流，类似 Kafka。
  - **Bitmap**：基于 String 的位操作，用于签到、在线状态。
  - **HyperLogLog**：基数统计，误差 0.81%，仅占 12KB。
  - **Geospatial**：地理位置存储和计算。

## 扩展知识

- **编码转换**：Redis 根据数据量和元素类型自动选择底层编码，以平衡内存和性能。例如 Hash 在元素少且值小时用 `ziplist`，超过阈值转为 `hashtable`。
- **对象结构**：Redis 所有值都封装为 `redisObject`，包含 `type`（逻辑类型）、`encoding`（底层编码）、`ptr`（指向实际数据）、`refcount`（引用计数）、`lru`（LRU 信息）。
- **ziplist 的连锁更新问题**：ziplist 中插入/删除元素可能导致连续多个 entry 的 `prevrawlen` 字段需要更新，引发 O(N²) 的最坏情况。Redis 5.0 引入 `listpack` 替代 ziplist 解决此问题。

## 对比表格

| 类型 | 底层编码 | 典型场景 | 时间复杂度（核心操作） |
|------|---------|---------|---------------------|
| String | int / embstr / raw | 缓存、计数器 | GET/SET: O(1) |
| Hash | ziplist / hashtable | 对象存储 | HGET/HSET: O(1) |
| List | quicklist | 消息队列、时间线 | LPUSH/RPUSH: O(1) |
| Set | intset / hashtable | 标签、集合运算 | SADD: O(1) |
| ZSet | ziplist / skiplist+hashtable | 排行榜 | ZADD: O(log N) |
| Stream | rax tree | 消息队列 | XADD: O(1) |
| Bitmap | String | 签到、布隆过滤器 | SETBIT: O(1) |
| HyperLogLog | 稀疏/密集编码 | 基数统计 | PFADD: O(1) |
