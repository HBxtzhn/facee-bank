- **网络因素**：主从节点间网络带宽不足、网络延迟高、网络抖动导致命令传输慢。
- **大命令阻塞**：执行 `KEYS *`、大范围 `ZRANGE`、大 Key 操作等耗时命令，阻塞复制线程。
- **全量同步开销**：RDB 生成耗时（fork 子进程时的 COW 内存拷贝）、RDB 文件传输耗时、从节点加载 RDB 耗时。
- **从节点负载过高**：从节点 CPU/内存/IO 资源不足，处理复制积压缓冲区（repl_backlog）的速度跟不上。
- **主节点写入过快**：主节点写入速度超过复制链路传输速度，导致复制偏移量（offset）差距持续扩大。
- **复制缓冲区溢出**：`repl-backlog-size` 设置过小，写入过快导致积压缓冲区溢出，触发全量同步。

## 扩展知识

**Redis 复制流程**：
1. 从节点发送 `PSYNC` 命令
2. 主节点判断能否增量同步（检查 repl_backlog 中 offset 对应的数据是否还在）
3. 能增量：直接发送 backlog 中 offset 之后的命令
4. 不能增量：fork 子进程生成 RDB → 传输 RDB → 传输 backlog 增量数据

**关键配置参数**：
- `repl-backlog-size`：复制积压缓冲区大小，默认 1MB。主从断线重连时用于增量同步
- `repl-backlog-ttl`：从节点断开后，积压缓冲区保留时间（默认 3600s）
- `repl-diskless-sync`：是否启用无盘复制（直接通过 socket 传输 RDB，不落盘）
- `repl-timeout`：复制超时时间（默认 60s）

**监控指标**：
- `INFO replication` 中的 `master_repl_offset` 和 `slave_repl_offset` 差值
- `master_link_status`：up/down 状态
- `repl_backlog_first_byte_offset` 和 `repl_backlog_size` 判断缓冲区是否充足

## 对比表格

| 原因类别 | 具体表现 | 排查方法 | 解决方案 |
|---------|---------|---------|---------|
| 网络问题 | ping 延迟高、丢包 | `ping`/`mtr` 网络诊断 | 优化网络拓扑，主从同机房 |
| 大命令 | 单条命令执行时间过长 | `SLOWLOG GET` 查看慢查询 | 拆分大 Key，避免 `KEYS *` |
| 全量同步 | offset 差距突增后恢复 | `INFO replication` 看 sync 次数 | 增大 `repl-backlog-size` |
| 从节点过载 | CPU 100%、IO wait 高 | `top`/`iostat` 监控 | 升级配置，减少从节点数量 |
| 写入过快 | offset 差距持续扩大 | 监控 offset 差值趋势 | 限流主节点写入，增加带宽 |
| 缓冲区溢出 | 频繁触发全量同步 | 查看 `sync_full` 计数 | 增大 `repl-backlog-size` |
