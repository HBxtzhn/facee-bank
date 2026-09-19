- **双编码结构**：Zset 根据数据量和元素大小自动选择编码：
  - **ListPack**（Redis 7.0+）/ **Ziplist**（旧版）：元素少且小时使用，紧凑内存布局
  - **SkipList + Dict**：元素多或大时使用，跳表负责有序操作，字典负责 O(1) 查分数
- **跳表（Skip List）**：多层链表结构，底层包含所有元素，上层为索引层。查找/插入/删除平均 O(log N)。每个节点有 1-32 层，层数通过随机算法决定（`zslRandomLevel()`，p=0.25）。
- **字典（Dict）**：Hash 表结构，`field` 为成员名，`value` 为分数（score）。用于 O(1) 根据成员名查分数。
- **数据一致性**：跳表和字典存储相同数据，任何更新操作同时修改两者，保证一致。

## 扩展知识

**跳表的结构**：
```c
typedef struct zskiplist {
    struct zskiplistNode *header, *tail;
    unsigned long length;
    int level;  // 当前最高层数
} zskiplist;

typedef struct zskiplistNode {
    sds ele;                    // 成员名
    double score;               // 分数
    struct zskiplistLevel {
        struct zskiplistNode *forward;  // 前进指针
        unsigned long span;             // 跨度（经过的节点数）
    } level[];                  // 层数组
    struct zskiplistNode *backward;     // 后退指针
} zskiplistNode;
```

**编码转换阈值**：
- `zset-max-listpack-entries`：默认 128，元素个数阈值
- `zset-max-listpack-value`：默认 64，元素值字节长度阈值
- 两个条件都满足时用 ListPack，任一超出则转为 SkipList + Dict

**跳表的操作**：
- **查找**：从最高层开始，如果下一节点 score < 目标 score，前进到下一节点；否则下降到下一层。最终在最底层定位到目标节点。
- **插入**：查找确定插入位置，随机生成层数，创建新节点，更新各层前后指针和 span。
- **删除**：查找定位节点，更新各层前后指针，如果最高层被删空则降低 level。

**范围查询（ZRANGEBYSCORE）**：
1. 在跳表中找到 score 范围的起点（O(log N)）
2. 沿最底层链表顺序遍历，收集范围内的元素（O(M)，M 为结果数量）
3. 总时间复杂度 O(log N + M)

## 对比表格

| 操作 | ListPack 编码 | SkipList + Dict 编码 |
|------|--------------|---------------------|
| 添加元素 | O(N) | O(log N) |
| 删除元素 | O(N) | O(log N) |
| 查分数 | O(N) | O(1)（Dict） |
| 按排名查 | O(N) | O(log N) |
| 范围查询 | O(N) | O(log N + M) |
| 内存占用 | 低（紧凑） | 较高（指针开销） |
| 适用数据量 | 小（<128 且值 <64B） | 大 |
