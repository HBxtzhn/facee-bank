- **常见瓶颈点**：
  1. **CPU 瓶颈**：复杂命令（如 `KEYS *`、大范围的 `ZRANGEBYSCORE`）、大 key 操作
  2. **内存瓶颈**：内存不足导致淘汰、内存碎片率高
  3. **网络瓶颈**：带宽打满、客户端连接数过多
  4. **磁盘 I/O 瓶颈**：RDB/AOF 持久化写入、主从复制全量同步
  5. **单线程瓶颈**：命令执行串行化，慢命令阻塞后续请求
- **排查手段**：
  - `INFO` 命令：查看内存、连接、命令统计等
  - `SLOWLOG GET`：查看慢查询日志
  - `redis-cli --latency`：检测延迟
  - `MONITOR`：实时监控命令（慎用，影响性能）
  - RedisInsight / Prometheus + Grafana：可视化监控

## 扩展知识

**优化策略**：

1. **避免慢命令**：
   - 禁用 `KEYS *`，使用 `SCAN` 替代
   - 避免大范围的集合操作
   - 使用 `UNLINK` 替代 `DEL` 删除大 key

2. **Pipeline 批量操作**：减少网络往返次数

3. **读写分离**：读操作走从节点，减轻主节点压力

4. **集群扩展**：Redis Cluster 水平扩展，分散负载

5. **本地缓存**：热点数据使用 Caffeine/Guava 本地缓存，减少 Redis 访问

6. **连接池优化**：合理配置连接池大小，避免连接争抢

7. **内存优化**：
   - 使用合适的数据结构（如 Hash 替代多个 String）
   - 设置合理过期时间
   - 调整 `maxmemory-policy`

8. **持久化优化**：
   - 主节点关闭持久化，从节点负责 RDB
   - AOF 使用 `everysec` 策略
   - 禁用自动 rewrite，手动在低峰期执行

## 对比表格

| 瓶颈类型 | 表现 | 排查方式 | 解决方案 |
|---------|------|---------|---------|
| CPU | 命令延迟高、QPS 下降 | `INFO cpu`、`SLOWLOG` | 避免慢命令、拆分复杂操作 |
| 内存 | OOM、淘汰频繁 | `INFO memory` | 优化数据结构、设置过期、扩容 |
| 网络 | 延迟高、超时 | `redis-cli --latency` | Pipeline、压缩、读写分离 |
| 磁盘 I/O | fork 慢、AOF rewrite 慢 | `INFO persistence` | 优化持久化策略、SSD |
| 单线程 | 阻塞、排队 | `SLOWLOG`、`MONITOR` | 避免大 key、异步删除 |
