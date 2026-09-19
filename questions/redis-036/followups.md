## 追问 1：Hash 的 ziplist 编码在什么情况下会转为 hashtable？

满足任一条件：① field 数量超过 `hash-max-ziplist-entries`（默认 512）；② 任一 field 的 value 长度超过 `hash-max-ziplist-value`（默认 64 字节）。转换不可逆（即使后续删除元素也不会退回 ziplist）。

## 追问 2：渐进式 rehash 的具体过程是什么？

① 分配 `ht[1]`（新大小的哈希表）；② 设置 `rehashidx = 0`，标记 rehash 开始；③ 后续每次对 Hash 的增删改查操作，除了操作 `ht[0]`，还将 `rehashidx` 指向的桶迁移到 `ht[1]`，`rehashidx++`；④ 全部迁移完成后，释放 `ht[0]`，`ht[1]` 变为 `ht[0]`，`rehashidx = -1`。

## 追问 3：HGETALL 和 HMGET 有什么区别？性能上有什么注意事项？

`HGETALL` 返回所有 field-value 对，数据量大时会阻塞主线程；`HMGET` 只返回指定 field，更可控。生产环境建议用 `HSCAN` 替代 `HGETALL` 进行渐进式遍历，避免大 key 阻塞。
