1. **核心概念**：脑裂（Split Brain）是指在分布式系统中，由于网络分区，集群被分割成两个或多个独立的部分，各部分都认为自己是主节点并对外提供服务，导致数据不一致。
2. **关键细节拆解**：
   - **Redis Cluster 的脑裂防护**：
     - Redis Cluster 使用 **quorum 机制** 防止脑裂
     - 主节点需要获得大多数 master 节点的投票才能完成故障转移
     - 配置项 `cluster-require-full-coverage` 控制部分节点不可用时的行为
   - **哨兵模式的脑裂防护**：
     - Sentinel 通过 quorum（`sentinel monitor <name> <ip> <port> <quorum>`）防止脑裂
     - 需要 quorum 个 Sentinel 同意才能执行 failover
   - **网络分区场景**：
     - 如果主节点所在分区没有足够票数，无法完成 failover，该分区的主节点继续服务
     - 另一分区会选举新主节点，此时可能出现两个"主节点"短暂并存
   - **`min-replicas-to-write` 配置**：主节点可以设置最小从节点副本数，当连接的从节点数量低于阈值时拒绝写入，防止分区中的旧主节点继续接受写入

## 扩展知识

- **Raft 协议的脑裂防护**：Redis Cluster 的故障转移基于 Raft 协议的变体，每个 epoch 相当于一个任期，节点在一个 epoch 内只能投一票，保证了同一 epoch 内只有一个新主节点。
- **`cluster-require-full-coverage`**：设为 `yes`（默认）时，如果集群无法覆盖所有 16384 个 slot，整个集群拒绝服务；设为 `no` 时，可用 slot 仍可服务。
- **脑裂后的数据丢失**：如果旧主节点在网络分区期间接受了写入，分区恢复后这些写入会被丢弃（新主节点的数据为准），客户端可能收到写入成功但数据实际丢失的情况。
- **`min-replicas-to-write` 和 `min-replicas-max-lag`**：这两个参数配合使用，确保主节点至少有 N 个从节点且延迟不超过 M 秒时才接受写入，有效防止脑裂导致的数据丢失。

## 对比表格

| 场景 | 哨兵模式 | Redis Cluster | 无防护的集群 |
|------|---------|--------------|-------------|
| 网络分区 | quorum 防护 | epoch + quorum 防护 | 可能出现脑裂 |
| 双主并存 | 短暂并存（quorum 阻止旧主） | 短暂并存（epoch 机制） | 持续并存 |
| 数据一致性 | 分区恢复后同步 | 分区恢复后丢弃旧主数据 | 无法保证 |
| 写入安全 | `min-replicas-to-write` | `min-replicas-to-write` | 无保障 |
