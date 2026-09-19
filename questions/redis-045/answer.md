1. **核心概念**：Redis Sentinel（哨兵）是 Redis 的高可用解决方案，用于监控主从节点、自动故障转移（failover）、提供配置发现和通知。
2. **关键细节拆解**：
   - **核心功能**：
     - **监控**：持续检测主从节点是否正常运行
     - **通知**：通过发布订阅机制通知管理员或其他客户端故障事件
     - **自动故障转移**：主节点不可用时，自动将从节点提升为新主节点
     - **配置中心**：客户端连接 Sentinel 获取当前主节点地址，故障转移后自动更新
   - **故障判定流程**：
     - **主观下线（SDOWN）**：单个 Sentinel 认为节点不可达（超过 `down-after-milliseconds` 未响应）
     - **客观下线（ODOWN）**：quorum 个 Sentinel 都认为主节点 SDOWN
     - **选举 Leader Sentinel**：通过 Raft 算法选举一个 Leader 执行故障转移
     - **故障转移**：Leader 选择最优从节点提升为主，通知其他从节点切换主节点
   - **部署要求**：至少 3 个 Sentinel 实例，quorum 通常设为 `(sentinel数/2)+1`

## 扩展知识

- **Raft 选举算法**：Sentinel 之间使用 Raft 协议选举 Leader，每个 Sentinel 发送 `SENTINEL is-master-down-by-addr` 进行投票，获得 quorum 票数的 Sentinel 成为 Leader 执行 failover。
- **从节点选择策略**：优先选择 `replica-priority` 最小的从节点；若优先级相同，选择复制偏移量最大的（数据最新）；若仍相同，选择 `runid` 最小的。
- **Sentinel 间的通信**：通过发布订阅机制，每个 Sentinel 订阅 `__sentinel__:hello` 频道交换信息和心跳。
- **客户端集成**：客户端（如 Jedis、Lettuce）连接 Sentinel 集群，定期获取主节点信息，故障转移后自动重连新主节点。

## 对比表格

| 特性 | 哨兵模式 | 主从复制 | Redis Cluster |
|------|---------|---------|--------------|
| 自动故障转移 | ✅ | ❌ | ✅ |
| 数据分片 | ❌ | ❌ | ✅ |
| 高可用 | 高 | 低 | 高 |
| 部署复杂度 | 中 | 低 | 高 |
| 适用场景 | 单主高可用 | 读写分离 | 大规模数据分片 |
| 节点数量限制 | 1 主多从 | 1 主多从 | 最多 1000 节点 |
