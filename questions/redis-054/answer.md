- **核心数据结构**：使用 `ZSET`（有序集合），底层由跳表（SkipList）+ 压缩列表/列表包实现。
- **关键命令**：
  - `ZADD key score member`：添加成员及分数
  - `ZINCRBY key increment member`：增加分数
  - `ZREVRANGE key start stop [WITHSCORES]`：按分数降序获取排名
  - `ZREVRANK key member`：获取某成员的排名（降序）
  - `ZRANGEBYSCORE key min max`：按分数范围查询
- **典型场景**：游戏积分排行榜、热搜榜、销量排行等。
- **时间复杂度**：添加/更新 O(logN)，查询排名 O(logN)，范围查询 O(logN+M)。

## 扩展知识

**按时间维度划分排行榜**：
- 日榜/周榜/月榜：使用不同 key，如 `rank:daily:20260723`、`rank:weekly:202630`
- 设置过期时间自动清理：`EXPIRE rank:daily:20260723 86400`

**同分排序处理**：
- ZSET 分数相同时按字典序排列，如果需要按时间先后排序，可以将分数设计为复合值：`score = 分数 * 10^10 + (MAX_TIMESTAMP - 当前时间戳)`
- 这样分数高的排前面，同分时先达到的排前面

**大规模排行榜优化**：
- 分片：按用户ID哈希分多个 ZSET，查询时聚合
- 缓存热点：排行榜数据适合放在 Redis Cluster 的主节点，避免跨 slot 查询

## 对比表格

| 方案 | 排序能力 | 时间复杂度 | 适用场景 |
|------|---------|-----------|---------|
| ZSET | 按分数排序，支持范围查询 | O(logN) | 排行榜首选 |
| LIST + SORT | 需手动排序 | O(NlogN) | 数据量小、一次性排序 |
| Hash + 外部排序 | 无内置排序 | O(NlogN) | 需要复杂排序逻辑 |
| 普通 SET | 无序 | - | 不适合排行榜 |
