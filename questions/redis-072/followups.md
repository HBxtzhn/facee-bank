## 追问 1：为什么 Redis Cluster 选择 16384 个 slot？

① 心跳包大小：节点间通过 gossip 协议交换 slot 位图，16384 bit = 2KB，压缩后更小。65536 需要 8KB，网络开销大；② 压缩效率：16384 的位图压缩比更高（通常压缩到几百字节）；③ 足够用：实际集群很少超过 1000 个主节点，16384 个 slot 分配粒度足够细；④ 设计权衡：在心跳包大小和分配粒度之间取平衡。

## 追问 2：Hash Tag 的工作原理是什么？有什么限制？

Hash Tag 允许通过 `{tag}` 语法控制 key 的 slot 分配。CRC16 只计算第一个 `{` 和对应的 `}` 之间的内容。如 `{user}:1` 和 `{user}:2` 都按 `user` 计算 CRC16，落在同一 slot。限制：① 只能有一个有效的 `{}`（取第一个 `{` 和它之后第一个 `}` 之间的内容）；② 如果没有 `}` 或有多个 `{}`，整个 key 参与计算；③ tag 相同时所有 key 落在同一 slot，可能导致数据倾斜。

## 追问 3：Cluster 扩容时 slot 迁移的过程是怎样的？

① 在源节点执行 `CLUSTER SETSLOT <slot> MIGRATING <dest>`，标记 slot 为迁出中；② 在目标节点执行 `CLUSTER SETSLOT <slot> IMPORTING <src>`，标记为迁入中；③ 使用 `MIGRATE` 命令逐批迁移 key（或 `CLUSTER SETSLOT` 批量操作）；④ 迁移期间，访问源节点的客户端收到 ASK 重定向到目标节点；⑤ 迁移完成后，在任意节点执行 `CLUSTER SETSLOT <slot> NODE <dest>` 广播新归属；⑥ 客户端收到 MOVED 更新路由表。
