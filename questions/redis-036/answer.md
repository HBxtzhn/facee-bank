- **Hash 类型**是 Redis 中的一种数据结构，用于存储字段-值（field-value）对的集合，类似编程语言中的 HashMap / Dictionary。
- **适用场景**：存储对象（如用户信息 `user:1001` → `{name: "Tom", age: 25}`），相比用多个 String 存储更节省内存且便于管理。
- **核心命令**：`HSET`/`HGET`（单字段读写）、`HMSET`/`HMGET`（多字段读写）、`HGETALL`（获取所有字段）、`HDEL`（删除字段）、`HINCRBY`（原子递增）。
- **底层编码**：
  - `ziplist`（压缩列表）：元素少（`hash-max-ziplist-entries` ≤ 512）且值短（`hash-max-ziplist-value` ≤ 64 字节）时使用，内存紧凑。
  - `hashtable`：超过阈值时自动转换，提供 O(1) 的查找性能。

## 扩展知识

- **ziplist 编码的 Hash**：field 和 value 交替存储在 ziplist 中，每次更新需要重新分配内存。适合小对象，但大对象或频繁更新场景应使用 hashtable。
- **渐进式 rehash**：Hash 使用 hashtable 编码时，扩容/缩容采用渐进式 rehash。维护两个 hashtable（`ht[0]` 和 `ht[1]`），在后续的每次增删改查操作中逐步迁移数据，避免一次性迁移的阻塞。
- **内存优化**：Hash 是 Redis 中最节省内存的类型之一（ziplist 编码下），因为 ziplist 是连续内存，没有指针开销和内存碎片。

## 对比表格

| 编码 | 触发条件 | 内存占用 | 查找复杂度 | 适用场景 |
|------|---------|---------|-----------|---------|
| ziplist | entries ≤ 512 且 value ≤ 64B | 低（连续内存） | O(N) | 小对象，字段少 |
| hashtable | 超过 ziplist 阈值 | 较高（指针+桶） | O(1) | 大对象，字段多 |
