## 追问 1：fork 子进程时主进程会阻塞多久？如何优化？

**标准答案**：fork 阻塞时间取决于页表大小，通常 10GB 内存耗时 100-500ms。优化方案：使用 `nohz` 内核参数减少中断；使用大页内存（Transparent Huge Pages 但 Redis 官方建议关闭 THP）；控制 Redis 内存大小；使用 `latency monitor` 监控 fork 耗时。

## 追问 2：COW 机制在什么情况下会导致内存暴涨？如何预防？

**标准答案**：当 fork 期间父进程有大量写操作（如 `FLUSHALL`、大批量 `SET`），大量内存页被修改导致 COW 频繁复制，内存可能翻倍。预防措施：避免在 RDB 生成期间执行大批量写操作；设置合理的 `maxmemory` 防止 OOM；监控 `used_memory_rss` 与 `used_memory` 的差值。

## 追问 3：如果 RDB 生成过程中子进程失败了怎么办？

**标准答案**：子进程失败不会损坏已有数据（旧的 RDB 文件不受影响），Redis 主进程会记录错误日志。如果是磁盘空间不足导致的失败，需要清理磁盘后重试。可以通过 `info persistence` 查看 `rdb_last_bgsave_status` 确认最近一次 RDB 生成是否成功。
