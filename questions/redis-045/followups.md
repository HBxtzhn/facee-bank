## 追问 1：Sentinel 为什么至少需要 3 个实例？

**标准答案**：Sentinel 使用 Raft 协议进行 Leader 选举和故障判定，需要满足 quorum 机制。3 个 Sentinel 可以容忍 1 个故障（quorum=2），保证在单个 Sentinel 宕机时仍能正常进行故障转移。2 个 Sentinel 无法在 1 个故障时形成多数票。

## 追问 2：Sentinel 选择从节点提升为主节点的策略是什么？

**标准答案**：首先排除不健康的从节点（断连、SDOWN），然后按以下优先级选择：1）`replica-priority` 值最小的（设为 0 表示不参与选举）；2）复制偏移量最大的（数据最新）；3）`runid` 字典序最小的。

## 追问 3：客户端如何感知主节点切换？

**标准答案**：客户端连接 Sentinel 集群，定期（如每 10 秒）发送 `SENTINEL get-master-addr-by-name <master-name>` 获取主节点地址。当发生 failover 后，Sentinel 会通过 pub/sub 通知客户端新的主节点地址。现代客户端（如 Lettuce）内置 Sentinel 支持，自动完成重连。
