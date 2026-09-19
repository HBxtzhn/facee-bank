## 追问 1：Redis 的 Hash 和 String 存储对象各有什么优劣？

Hash 可以单独读写某个字段（`HGET field`），节省内存（ziplist 编码紧凑）；String 存储整个 JSON 序列化后的对象，读写需要整体序列化/反序列化。Hash 适合字段多且需要部分读写的场景；String 适合整体读写或需要原子操作的场景。

## 追问 2：ziplist 和 listpack 有什么区别？

ziplist 中每个 entry 记录前一个 entry 的长度（`prevrawlen`），插入/删除可能导致连锁更新。listpack 取消了 `prevrawlen`，每个 entry 独立编码，不会连锁更新，但遍历只能从头开始。

## 追问 3：HyperLogLog 的原理是什么？精度如何保证？

基于概率算法，利用哈希值的位模式估算基数。将元素哈希后观察前导零的个数，利用概率统计估算不同值的数量。16384 个寄存器仅需 12KB，标准误差 0.81%。不支持获取具体元素，只能估算基数。
