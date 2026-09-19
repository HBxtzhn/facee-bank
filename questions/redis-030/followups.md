## 追问 1：为什么 Redis Cluster 选择 16384 个槽而不是 65536 个？

① 节点心跳包中需要携带槽信息，16384 个槽的 bitmap 仅需 2KB，65536 需要 8KB，心跳包过大影响带宽；② Redis Cluster 建议节点数不超过 1000，16384 个槽足够分配；③ 压缩比更高，减少网络传输开销。

## 追问 2：集群中进行在线扩容的流程是什么？

① 新节点加入集群；② 将源节点的部分槽迁移到新节点（`CLUSTER SETSLOT <slot> IMPORTING <node-id>` → `MIGRATING` → 逐 key 迁移 → `SETSLOT NODE`）；③ 迁移过程中遇到 key 不在当前节点则返回 `ASK` 重定向；④ 迁移完成后更新所有节点的路由表。

## 追问 3：hash tag 是什么？如何使用？

当 key 中包含 `{...}` 时，Redis Cluster 只对花括号内的子串计算哈希，例如 `user:{1001}:name` 和 `user:{1001}:age` 会落入同一槽。用于保证相关 key 在同一节点上，支持多 key 操作（如 `MGET`、事务）。
