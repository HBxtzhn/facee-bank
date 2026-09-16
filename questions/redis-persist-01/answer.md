| 维度 | RDB | AOF |
|---|---|---|
| 内容 | 某一时刻的数据快照 | 写命令日志 |
| 体积 | 小 | 大 |
| 恢复速度 | 快 | 慢 |
| 数据安全性 | 可能丢最后一次快照后的数据 | 取决于 `appendfsync` |
| 对性能影响 | fork 时有写时复制开销 | 追加写，影响较小 |

## appendfsync 三档

- `always`：每条命令 fsync，最安全、性能最差；
- `everysec`（默认）：每秒 fsync，最多丢 1 秒；
- `no`：交给操作系统，可能丢较多。

## 混合持久化（Redis 4.0+）

`aof-use-rdb-preamble yes`：AOF 重写时前半部分写 RDB 格式、后半部分追加增量命令 ⇒ 兼顾**恢复速度**与**数据安全**。生产推荐开启。

## 线上建议

```text
appendonly yes
appendfsync everysec
aof-use-rdb-preamble yes
save 900 1   # 保留 RDB 作为冷备
```

注意 fork 的代价：内存越大、写时复制页越多，fork 停顿越明显（大实例可达数百毫秒），应避开业务高峰做 BGSAVE。
