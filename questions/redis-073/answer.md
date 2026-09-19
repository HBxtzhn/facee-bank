- **渐进式 Rehash**：Redis 的 dict 在扩容时不一次性迁移所有数据，而是在每次增删改查时分批迁移少量桶（bucket），将 O(N) 的 rehash 操作分散到多次 O(1) 操作中，避免阻塞主线程。
- **SDS（Simple Dynamic String）**：自定义字符串结构，记录 `len` 和 `free` 字段，O(1) 获取长度、二进制安全、减少内存重分配（预分配 + 惰性释放）。
- **跳表（Skip List）实现 Zset**：用多层链表实现 O(log N) 查找/插入/删除，比平衡树实现更简单，范围查询更高效。
- **整数集合（Intset）**：当 Set 全部为整数且数量较少时，使用有序数组存储，二分查找 O(log N)，内存紧凑无指针开销。
- **对象共享**：0-9999 的整数对象预创建并共享（`redisObject.refcount`），节省内存。

## 扩展知识

**渐进式 Rehash 详解**：
```c
typedef struct dict {
    dictEntry **table;  // hash 表数组
    dictType *type;     // 类型特定函数
    unsigned long size; // hash 表大小
    unsigned long sizemask;
    unsigned long used; // 已使用桶数
    rehashingstate rehash; // rehash 状态
} dict;
```
- `rehashidx`：记录当前迁移到哪个桶，-1 表示未在 rehash
- 每次操作 dict 时，调用 `dictRehashStep()` 迁移一个桶
- 后台定时器也会触发批量迁移（`dictRehashMilliseconds`）
- 缩容条件：元素数 < size * 10% 且 used < 4（最小大小）

**SDS 的预分配策略**：
- 修改后 len < 1MB：预分配 len * 2 的空间
- 修改后 len >= 1MB：预分配 len + 1MB 的空间
- 惰性释放：缩短字符串时不立即释放多余空间，存入 `free` 字段供后续使用

**Redis 对象系统（redisObject）**：
```c
typedef struct redisObject {
    unsigned type:4;    // 类型（string/list/hash/set/zset）
    unsigned encoding:4; // 编码（raw/intset/hashtable/ziplist等）
    unsigned lru:24;    // LRU 淘汰信息
    int refcount;       // 引用计数（对象共享）
    void *ptr;          // 指向实际数据
} robj;
```
- `type + encoding` 实现多态：同一逻辑类型可用不同底层编码
- 根据数据量自动转换编码（如 hash 从 listpack 转为 hashtable）

## 对比表格

| 设计 | 解决的问题 | 核心思想 | 效果 |
|------|-----------|---------|------|
| 渐进式 Rehash | 一次性 rehash 阻塞主线程 | 分批次迁移，每次 O(1) | 避免延迟尖刺 |
| SDS | C 字符串 O(N) 求长、不安全 | 记录 len/free，预分配 | O(1) 求长，减少分配 |
| 跳表 | Zset 需要有序 + 范围查询 | 多层链表，概率平衡 | O(log N)，实现简单 |
| Intset | 小整数集合内存开销大 | 有序数组 + 二分查找 | 内存紧凑，查找 O(log N) |
| 对象共享 | 频繁创建相同对象浪费内存 | 引用计数 + 预创建 | 节省内存，减少分配 |
| 编码转换 | 不同数据规模需要不同结构 | type + encoding 多态 | 空间时间最优平衡 |
