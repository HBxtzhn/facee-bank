- **ListPack（紧凑列表）**：Redis 7.0 引入，用于替代 Ziplist。解决了 Ziplist 的连锁更新（Cascading Update）问题。
- **核心改进**：每个 entry 只存储自身长度，不存储前驱节点长度（去掉 `prevlen` 字段）。因此任何节点的增删改不会影响其他节点的编码。
- **结构布局**：`| bytes(4B) | num_elements(2B) | entry1 | entry2 | ... | end(1B) |`
- **应用场景**：替代 Ziplist 用于 Hash（`hash-max-listpack-size`）、Zset（`zset-max-listpack-size`）、List（Quicklist 的每个节点内部使用 ListPack）等紧凑编码场景。

## 扩展知识

**ListPack Entry 编码**：
- 首字节同时编码长度和数据类型：
  - `0xxxxxxx`：1 字节，存储 0-127 的整数
  - `10xxxxxx`：2 字节，存储 14 位整数
  - `110xxxxx`：3 字节，存储 21 位整数
  - `1110xxxx`：5 字节，存储 32 位整数
  - `11110000`：13 字节，存储 64 位整数
  - `1111xxxx`：字符串编码，xxxx 为长度前缀

**与 Ziplist 的对比**：
- Ziplist 支持双向遍历（通过 `prevlen` 回退），ListPack 只支持单向遍历（从头到尾）
- 但实际上 ListPack 可以通过记录每个 entry 的起始偏移量来支持反向遍历（内部实现）
- ListPack 的内存占用与 Ziplist 几乎相同（省去了 `prevlen`，但 entry header 略有调整）

**为什么选择 ListPack 而非其他方案**：
- 保持连续内存布局，缓存友好
- 消除连锁更新的最坏情况
- 编码效率与 Ziplist 相当
- 实现复杂度可控

## 对比表格

| 特性 | Ziplist | ListPack |
|------|---------|----------|
| 前驱长度字段 | 有（`prevlen`） | 无 |
| 连锁更新风险 | 有 | 无 |
| 双向遍历 | 支持（通过 prevlen） | 支持（通过偏移量记录） |
| 内存布局 | 连续内存 | 连续内存 |
| 缓存友好性 | 好 | 好 |
| 编码效率 | 高 | 高（基本相同） |
| 引入版本 | Redis 2.x | Redis 7.0 |
| 最大容量 | 无硬限制（实际受内存约束） | 1GB（`bytes` 字段 4 字节） |
