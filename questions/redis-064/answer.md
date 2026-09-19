- **List 特点**：有序、可重复、支持两端操作（头尾插入/弹出），底层由 QuickList（ziplist + linkedlist）实现。
- **核心命令**：
  - **插入**：
    - `LPUSH key value1 value2 ...`：从左侧（头部）插入
    - `RPUSH key value1 value2 ...`：从右侧（尾部）插入
    - `LINSERT key BEFORE|AFTER pivot value`：在指定元素前/后插入
  - **弹出**：
    - `LPOP key [count]`：从左侧弹出（Redis 6.2+ 支持 count）
    - `RPOP key [count]`：从右侧弹出
    - `BLPOP key [key ...] timeout`：阻塞式左弹出
    - `BRPOP key [key ...] timeout`：阻塞式右弹出
  - **查询**：
    - `LRANGE key start stop`：获取指定范围的元素
    - `LINDEX key index`：获取指定索引的元素
    - `LLEN key`：获取列表长度
  - **修改**：
    - `LSET key index value`：设置指定索引的值
    - `LREM key count value`：删除指定个数的指定值
    - `LTRIM key start stop`：裁剪列表，只保留指定范围
  - **移动**：
    - `RPOPLPUSH source destination`：从源列表右侧弹出并推入目标列表左侧
    - `BRPOPLPUSH source destination timeout`：阻塞版本

## 扩展知识

**List 底层实现演进**：
- Redis 3.2 之前：ziplist（小数据量）或 linkedlist（大数据量）
- Redis 3.2+：quicklist = 由双向链表连接的多个 ziplist 组成的复合结构
- QuickList 兼顾了内存效率（ziplist 紧凑）和操作效率（linkedlist 快速定位）

**阻塞命令的应用**：
- `BLPOP`/`BRPOP` 实现简单消息队列：生产者 `LPUSH`，消费者 `BRPOP` 阻塞等待
- 超时机制避免无限等待
- 支持监听多个 key，任一 key 有数据即返回

**List 的常见用途**：
- 消息队列（LPUSH + BRPOP）
- 最新文章列表（LPUSH + LTRIM 限制长度）
- 时间线（Timeline）

## 对比表格

| 命令 | 功能 | 时间复杂度 | 阻塞 |
|------|------|-----------|------|
| LPUSH/RPUSH | 左/右插入 | O(1) | 否 |
| LPOP/RPOP | 左/右弹出 | O(1) | 否 |
| BLPOP/BRPOP | 阻塞式弹出 | O(1) | 是 |
| LRANGE | 范围查询 | O(S+N) | 否 |
| LINDEX | 索引查询 | O(N) | 否 |
| LLEN | 获取长度 | O(1) | 否 |
| LINSERT | 指定位置插入 | O(N) | 否 |
| LREM | 删除指定值 | O(N) | 否 |
| LTRIM | 裁剪列表 | O(N) | 否 |
