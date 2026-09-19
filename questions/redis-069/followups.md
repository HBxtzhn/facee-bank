## 追问 1：Redis Cluster 为什么设计 16384 个 slot 而不是 65536 个？

① 心跳包优化：每个节点的心跳包需要携带 slot 信息的位图（bitmap），16384 个 slot 只需 2KB（16384/8），65536 需要 8KB。节点间频繁通信，心跳包越小越好；② 压缩：数据在传输时会压缩，16384 的 bitmap 压缩后通常只有几百字节；③ 实际场景中 16384 个 slot 足够分配，且压缩比更优。

## 追问 2：Cluster 模式下如何处理跨 slot 的多 key 操作？

Redis Cluster 默认不支持跨 slot 的多 key 操作（如 `MGET`、事务、Lua 脚本涉及多个 key）。解决方案：① 使用 hash tag，如 `{user}:1` 和 `{user}:2`，它们会被分配到同一个 slot（只计算 `{}` 内的部分做 CRC16）；② 客户端做多次单 key 操作（但失去原子性）；③ 使用 Lua 脚本 + hash tag 保证原子性。

## 追问 3：Sentinel 模式下，主节点故障转移后，旧主节点恢复会怎样？

旧主节点恢复后会以从节点身份加入集群，同步新主节点的数据。它不会自动恢复为主节点。Sentinel 已经完成了角色切换，旧主节点通过 `SLAVEOF` 命令（Sentinel 发送的 `slaveof no one` 给新主，`slaveof new_master_ip new_master_port` 给旧主）降级为从节点。
