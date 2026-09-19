## 追问 1：什么场景下应该选择 Memcached 而不是 Redis？

① 纯缓存场景，不需要持久化和复杂数据结构；② 多核 CPU 且需要极致多线程性能（Redis 6.0 前）；③ 存储大量简单的 KV 数据，Memcached 的 slab 机制内存碎片更少；④ 已有 Memcached 集群且运行稳定，无迁移必要。

## 追问 2：Redis 的内存淘汰策略和 Memcached 的 LRU 有什么区别？

Redis 提供 8 种淘汰策略（noeviction / allkeys-lru / volatile-lru / allkeys-lfu / volatile-lfu / allkeys-random / volatile-random / volatile-ttl），可按需选择；Memcached 仅使用 LRU，且是全局 LRU（Redis 是近似 LRU）。Redis 的 LFU 策略基于访问频率，更适合热点数据缓存。

## 追问 3：两者在分布式场景下的表现有何不同？

Redis Cluster 提供原生分片和高可用，数据迁移、故障转移自动化；Memcached 的分布式依赖客户端实现一致性哈希，节点故障时该节点数据丢失（无副本），需要应用层处理。Redis 更适合需要高可用的生产环境。
