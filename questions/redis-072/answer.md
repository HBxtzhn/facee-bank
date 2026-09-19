- **Hash Slot 机制**：Redis Cluster 将数据分为 16384 个 hash slot，每个主节点负责一部分 slot。key 到 slot 的映射：`slot = CRC16(key) % 16384`。
- **Slot 到节点的映射**：集群维护一张 slot → node 的路由表。每个节点都知道所有 slot 的归属，客户端连接任意节点即可获取路由信息。
- **重定向机制**：客户端访问了错误节点，该节点返回 `MOVED <slot> <ip:port>`（永久迁移）或 `ASK <slot> <ip:port>`（迁移中临时重定向）。
- **Hash Tag**：使用 `{tag}` 语法控制 key 的 slot 分配。`CRC16` 只计算 `{}` 内的部分，如 `{user}:1` 和 `{user}:2` 会落在同一 slot。

## 扩展知识

**CRC16 算法**：
- Redis Cluster 使用 CRC16-CCITT 变体（多项式 0x1021）
- 计算速度快，分布均匀
- 结果对 16384 取模，保证 slot 编号在 0-16383 范围内

**路由表更新机制**：
- 每个节点通过 gossip 协议定期交换配置信息（configEpoch）
- 客户端缓存 slot → node 映射表，收到 MOVED 时更新本地缓存
- 集群配置变更（如扩缩容）时，configEpoch 递增，节点间传播新配置

**MOVED vs ASK 的区别**：
- `MOVED`：slot 已永久迁移到新节点，客户端应更新本地路由表
- `ASK`：slot 正在迁移中（MIGRATING/IMPORTING 状态），客户端本次请求重定向到新节点，但不更新路由表（迁移可能失败回滚）
- ASK 重定向前需先发送 `ASKING` 命令，告知目标节点允许访问正在迁移的 slot

**客户端实现**：
- 智能客户端（如 Jedis Cluster、Lettuce）本地维护路由表，自动处理 MOVED/ASK
- 非智能客户端需要应用层处理重定向逻辑

## 对比表格

| 步骤 | 操作 | 说明 |
|------|------|------|
| 1 | `CRC16(key)` | 计算 key 的 CRC16 哈希值 |
| 2 | `% 16384` | 取模得到 slot 编号（0-16383） |
| 3 | 查路由表 | 根据 slot 编号找到负责的主节点 |
| 4 | 发送命令 | 向目标节点发送命令 |
| 5a | 正确节点 | 正常执行并返回结果 |
| 5b | 错误节点（永久迁移） | 返回 MOVED，客户端更新路由表并重试 |
| 5c | 错误节点（迁移中） | 返回 ASK，客户端发送 ASKING 后重定向到新节点 |
