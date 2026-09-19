## 追问 1：如何判断主从延迟的严重程度？

通过 `INFO replication` 获取 `master_repl_offset` 和每个从节点的 `slave_repl_offset`，计算差值。差值代表从节点落后主节点的字节数。可以设置告警阈值（如差值超过 10MB 告警）。也可以通过 Redis 的 `latency` 子系统监控复制延迟事件。

## 追问 2：全量同步的耗时主要花在哪里？如何优化？

三个阶段：① 主节点 fork 子进程（阻塞主线程，取决于内存大小和 THP 设置）；② 生成并传输 RDB 文件（取决于数据量和网络带宽）；③ 从节点加载 RDB（需要清除旧数据，取决于数据量）。优化手段：开启 `repl-diskless-sync yes` 跳过磁盘 IO；设置 `rdb-del-sync-files yes` 同步后删除 RDB；控制合理的数据量。

## 追问 3：主从复制是异步的，如何保证数据不丢失？

原生主从复制是异步的，主节点不等待从节点确认就返回客户端。如需更强一致性：① 使用 `WAIT` 命令让主节点阻塞等待指定数量从节点确认；② 使用 Redis Sentinel + `min-slaves-to-write` 配置（写入时至少有 N 个从节点连接且延迟 < M 秒）；③ 使用 Redis Cluster 的 `WAIT` 机制。但都无法做到强一致，真正强一致需要外部方案（如 Redlock + 业务补偿）。
