## 追问 1：Redis 为什么不再支持虚拟内存？如果内存不够怎么办？

VM 被移除的核心原因是延迟不可预测。磁盘 IO 的延迟是内存的十万倍以上，swap 操作会导致部分请求延迟从微秒级飙升到毫秒级，违反 Redis 低延迟的设计哲学。内存不够时的解决方案：① 设置合理的 `maxmemory` 和淘汰策略（如 allkeys-lfu）；② 优化数据结构（用 hash 代替多个 string，用小整数编码等）；③ 水平扩展（Cluster 分片）；④ 冷热数据分离（热数据 Redis + 冷数据 RocksDB）。

## 追问 2：Redis 的内存淘汰策略有哪些？各适用什么场景？

8 种策略：① `noeviction`：不淘汰，写入报错（默认）；② `allkeys-lru`：所有 key 中淘汰最近最少使用的；③ `volatile-lru`：仅淘汰设置了过期时间的 key；④ `allkeys-lfu`：所有 key 中淘汰最不常用的（4.0+）；⑤ `volatile-lfu`：仅淘汰设置了过期时间的 key 中最不常用的；⑥ `allkeys-random`：随机淘汰；⑦ `volatile-random`：随机淘汰有过期时间的 key；⑧ `volatile-ttl`：淘汰 TTL 最短的 key。推荐：缓存场景用 `allkeys-lfu`，有精确过期策略用 `volatile-lru`。

## 追问 3：如何估算 Redis 存储一定量数据需要多少内存？

使用 `redis-cli --bigkeys` 和 `MEMORY USAGE key` 分析实际内存占用。考虑因素：① 每个 key 的元数据开销（约 72 字节/对象）；② 数据结构 overhead（如 hashtable 的指针、dictEntry 等）；③ 内存分配器的对齐和碎片（jemalloc 碎片率约 1.1-1.3）；④ 客户端连接、复制缓冲区等固定开销。一般实际内存是纯数据大小的 1.5-3 倍。可以使用 `redis-rdb-tools` 分析 RDB 文件估算内存。
