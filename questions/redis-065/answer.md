- **队列（FIFO - 先进先出）**：
  - 使用 List 的 `LPUSH` + `RPOP`（或 `RPUSH` + `LPOP`）
  - 一端插入，另一端弹出，保证先进先出
  - 阻塞版本：`LPUSH` + `BRPOP`，实现消息队列
- **栈（LIFO - 后进先出）**：
  - 使用 List 的 `LPUSH` + `LPOP`（或 `RPUSH` + `RPOP`）
  - 同一端插入和弹出，保证后进先出
- **核心原理**：List 支持两端操作，通过组合不同的推入/弹出方向实现不同数据结构。

## 扩展知识

**队列实现详解**：

```
# 生产者
LPUSH queue "task1"
LPUSH queue "task2"
LPUSH queue "task3"

# 消费者（阻塞式）
BRPOP queue 0
# 返回顺序：task1 → task2 → task3（FIFO）
```

**栈实现详解**：

```
# 压栈
LPUSH stack "a"
LPUSH stack "b"
LPUSH stack "c"

# 弹栈
LPOP stack
# 返回顺序：c → b → a（LIFO）
```

**可靠消息队列实现**：
- 简单队列的问题：消费者 `RPOP` 后如果处理失败，消息丢失
- 解决方案：使用 `BRPOPLPUSH queue processing timeout`
  - 消息从 `queue` 弹出并推入 `processing` 列表（备份）
  - 处理完成后从 `processing` 中删除
  - 如果消费者崩溃，消息仍在 `processing` 中，可以被重新处理

**更高级的队列方案**：
- **Redis Stream**（Redis 5.0+）：支持消费者组、消息确认、历史回溯，是真正的消息队列
- **Redisson 分布式队列**：提供 BlockingQueue、DelayedQueue、PriorityQueue 等 Java 接口实现

## 对比表格

| 数据结构 | 推入命令 | 弹出命令 | 特性 | 应用场景 |
|---------|---------|---------|------|---------|
| 队列（FIFO） | LPUSH | RPOP / BRPOP | 先进先出 | 消息队列、任务调度 |
| 栈（LIFO） | LPUSH | LPOP | 后进先出 | 撤销操作、浏览器后退 |
| 延迟队列 | ZADD（score=时间戳） | ZRANGEBYSCORE | 按时间弹出 | 定时任务、延迟重试 |
| Stream | XADD | XREAD/XREADGROUP | 消费者组、ACK | 可靠消息队列 |
