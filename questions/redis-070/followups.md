## 追问 1：ListPack 如何在不存储 prevlen 的情况下支持反向遍历？

ListPack 在内存中是连续排列的，虽然每个 entry 不显式存储前驱长度，但 Redis 在遍历 ListPack 时会维护一个偏移量数组（或在需要时从头正向遍历到目标位置）。对于从尾部反向遍历的场景，ListPack 的每个 entry 头部编码了当前 entry 的长度，可以通过当前 entry 的起始地址减去前一个 entry 的长度来定位前一个 entry。实际上 ListPack 的 entry 编码中包含了足够的信息来支持反向导航。

## 追问 2：ListPack 的最大容量限制是多少？为什么？

ListPack 使用 4 字节存储总字节数（`bytes` 字段），因此最大容量为 2^32 - 1 字节（约 4GB）。但实际使用中，Redis 配置了 `listpack-max-size`（默认 1GB），超过此限制会触发转换（如 Hash 转为 hashtable 编码）。此外，ListPack 使用 2 字节存储元素个数（`num_elements`），最多表示 65535 个元素，超过此值设为 0xFFFF，需遍历计算实际数量。

## 追问 3：从 Ziplist 迁移到 ListPack，对现有业务有影响吗？

基本无影响。ListPack 是 Ziplist 的内部实现替换，对外暴露的数据结构和命令完全一致。迁移后：① 消除了连锁更新导致的延迟尖刺；② 内存占用基本不变；③ 性能略有提升（无连锁更新的开销）。唯一的差异是 ListPack 有 1GB 的大小限制和 65535 的元素个数阈值，但这些阈值在正常使用中几乎不会触及。
