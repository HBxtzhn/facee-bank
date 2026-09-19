## 追问 1：线上 Redis 突然变慢，如何排查？

① 检查 `SLOWLOG GET 20` 看是否有慢命令；② 检查 `INFO memory` 看内存是否打满、碎片率是否过高；③ 检查 `INFO clients` 看连接数是否异常；④ 检查 `INFO stats` 看 rejected_connections 是否增加；⑤ 检查是否有大 key 操作或 `KEYS *` 命令；⑥ 检查持久化（bgsave/bgrewriteaof）是否在执行；⑦ 检查网络延迟和带宽。

## 追问 2：Redis 内存碎片率高怎么处理？

内存碎片率 = `used_memory_rss / used_memory`，正常范围 1~1.5。过高时：① Redis 4.0+ 开启自动碎片整理（`activedefrag yes`）；② 重启 Redis（最彻底但影响可用性）；③ 避免频繁的小 key 创建和删除。

## 追问 3：如何做到 Redis 高可用？

① 主从复制 + 哨兵模式（Sentinel）实现自动故障转移；② Redis Cluster 实现分片 + 高可用；③ 客户端层面配合重试机制和超时设置；④ 使用 Codis/Twemproxy 等代理方案（适合老架构）。
