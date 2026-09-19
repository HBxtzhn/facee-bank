## 追问 1：RDB 的 fork 过程中，如果 Redis 内存很大，会有什么问题？

**标准答案**：fork 操作本身是 O(1) 的（只复制页表），但如果父进程在 fork 期间有大量写操作，COW 会导致大量内存页被复制，极端情况下内存使用量可能翻倍。Linux 的 `overcommit_memory` 配置不当可能导致 fork 失败。建议设置 `vm.overcommit_memory=1`，并控制 RDB 生成期间的写流量。

## 追问 2：AOF 重写期间新的写命令如何处理？

**标准答案**：AOF 重写由子进程执行，子进程根据内存数据生成新 AOF 文件。同时父进程继续接收写命令，这些命令既写入旧 AOF 文件，也写入一个 `aof_rewrite_buf`（重写缓冲区）。重写完成后，父进程将缓冲区中的增量命令追加到新 AOF 文件末尾，然后用 `rename` 原子替换旧文件。

## 追问 3：生产环境你会怎么配置持久化？

**标准答案**：推荐开启混合持久化（Redis 4.0+），设置 `appendonly yes`、`aof-use-rdb-preamble yes`、`appendfsync everysec`。同时保留 RDB 用于定期备份（如每天 `bgsave` 一次并上传到远程存储）。这样既保证数据安全（最多丢 1 秒），又有较快的恢复速度。
