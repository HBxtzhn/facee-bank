## 追问 1：为什么不建议存大 Value？

① Redis 单线程模型，操作大 value 会阻塞其他命令执行；② 大 key 删除时耗时长（除非用 UNLINK）；③ Cluster 模式下导致数据倾斜；④ 网络传输延迟增大；⑤ 内存碎片化加剧。

## 追问 2：如何发现大 Key？

① `redis-cli --bigkeys`：扫描并统计各类型的大 key；② `MEMORY USAGE key`：查看单个 key 的内存占用；③ RDB 分析工具（如 redis-rdb-tools）；④ 监控平台（如 RedisInsight）实时检测。

## 追问 3：需要存储超过 512MB 的数据怎么办？

不应该用 Redis 存储超大 value。应该：① 将数据拆分到多个 key；② 使用对象存储（如 MinIO/S3）存大文件，Redis 只存元数据或引用；③ 考虑使用其他存储引擎（如 HBase）。
