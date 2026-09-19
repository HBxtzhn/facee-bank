- 当 Redis 内存使用达到 `maxmemory` 上限时，根据配置的淘汰策略删除 key，为新数据腾出空间。
- **8 种策略**：
  - `noeviction`：不淘汰，写入时返回错误（默认）。
  - `allkeys-lru`：从所有 key 中淘汰最近最少使用的 key。
  - `volatile-lru`：从设置了过期时间的 key 中淘汰 LRU key。
  - `allkeys-lfu`：从所有 key 中淘汰最不常用的 key（Redis 4.0+）。
  - `volatile-lfu`：从设置了过期时间的 key 中淘汰 LFU key。
  - `allkeys-random`：从所有 key 中随机淘汰。
  - `volatile-random`：从设置了过期时间的 key 中随机淘汰。
  - `volatile-ttl`：从设置了过期时间的 key 中淘汰剩余 TTL 最短的 key。

## 扩展知识

- **近似 LRU**：Redis 不维护全局 LRU 链表（内存开销大），而是每次随机采样 N 个 key（`maxmemory-samples`，默认 5），淘汰其中最久未访问的。N 越大越接近真实 LRU，但消耗更多 CPU。
- **LFU 实现**：Redis 的 LFU 基于对数计数器，每个 key 维护 8-bit 的 `lfu_log2` 值，表示访问频率的对数。同时考虑时间衰减（`lfu_decay_time`），避免历史热点 key 永远不被淘汰。
- **策略选择建议**：
  - 缓存场景：推荐 `allkeys-lfu`（按访问频率）或 `allkeys-lru`（按最近访问）。
  - 部分 key 需要持久化：使用 `volatile-*` 系列，只淘汰可丢弃的 key。
  - 不确定：`allkeys-lru` 是最安全的选择。

## 对比表格

| 策略 | 淘汰范围 | 淘汰依据 | 适用场景 |
|------|---------|---------|---------|
| noeviction | 不淘汰 | - | 不允许数据丢失 |
| allkeys-lru | 所有 key | 最近最少使用 | 通用缓存 |
| volatile-lru | 有过期时间的 key | 最近最少使用 | 部分 key 需持久化 |
| allkeys-lfu | 所有 key | 访问频率最低 | 热点明显的缓存 |
| volatile-lfu | 有过期时间的 key | 访问频率最低 | 部分 key 需持久化 |
| allkeys-random | 所有 key | 随机 | 无明确热点 |
| volatile-random | 有过期时间的 key | 随机 | 无明确热点 |
| volatile-ttl | 有过期时间的 key | TTL 最短 | 即将过期的 key 优先淘汰 |
