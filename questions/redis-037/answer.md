- **数据结构**：Redis 支持 String、Hash、List、Set、ZSet、Stream 等多种类型；Memcached 仅支持简单的 Key-Value（String）。
- **持久化**：Redis 支持 RDB 快照和 AOF 日志持久化；Memcached 纯内存，重启数据丢失。
- **集群/高可用**：Redis 原生支持主从复制、哨兵、Cluster 集群；Memcached 依赖客户端一致性哈希实现分布式，无原生高可用方案。
- **内存管理**：Redis 支持内存淘汰策略和数据过期；Memcached 使用 LRU 淘汰，不支持持久化级别的过期控制。
- **线程模型**：Redis 6.0 前为单线程，6.0+ 引入 I/O 多线程；Memcached 天生多线程，多核利用率更高。
- **发布订阅/Lua**：Redis 支持 Pub/Sub、Lua 脚本、事务；Memcached 不支持。

## 扩展知识

- **性能对比**：单线程下两者性能相当（10w+ QPS）；多核场景下 Memcached 多线程优势明显，但 Redis 6.0+ 多线程也弥补了此差距。
- **内存效率**：Redis 的 ziplist、intset 等紧凑编码在小数据场景下比 Memcached 更省内存；Memcached 的 slab allocation 机制可有效减少内存碎片。
- **适用场景**：Redis 功能丰富，适合需要持久化、复杂数据结构、高可用的场景；Memcached 适合纯缓存、多核高并发、简单 KV 场景。

## 对比表格

| 特性 | Redis | Memcached |
|------|-------|-----------|
| 数据类型 | String/Hash/List/Set/ZSet/Stream 等 | 仅 Key-Value（String） |
| 持久化 | RDB + AOF | 不支持 |
| 集群 | 原生 Cluster / 哨兵 / 主从 | 客户端一致性哈希 |
| 线程模型 | 单线程（6.0+ I/O 多线程） | 多线程 |
| 内存管理 | 多种淘汰策略 + 紧凑编码 | Slab Allocation + LRU |
| 事务 | 支持（WATCH + MULTI） | 不支持 |
| Lua 脚本 | 支持 | 不支持 |
| 发布订阅 | 支持 | 不支持 |
| 数据过期 | 支持（精确到 key） | 支持（精确到 key） |
| 内存效率 | 高（紧凑编码） | 中（slab 预分配） |
