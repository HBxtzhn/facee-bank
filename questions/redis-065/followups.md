## 追问 1：如何保证消息队列的消息不丢失？

使用 `BRPOPLPUSH queue backup timeout`：消息从工作队列弹出后立即进入备份队列，处理成功后从备份队列删除（`LREM`）。如果消费者崩溃，备份队列中的消息可以被其他消费者重新获取。更可靠的方案是使用 Redis Stream 的消费者组和 ACK 机制。

## 追问 2：Redis Stream 和 List 实现的队列有什么区别？

List 队列功能简单，不支持消费者组、消息确认、历史回溯。Redis Stream 支持：① 消费者组（多个消费者分工处理）；② 消息 ACK（处理完确认后删除）；③ 消息历史（可回溯查看）；④ 按 ID 范围读取。Stream 是 Redis 5.0+ 推荐的队列方案。

## 追问 3：如何实现延迟队列？

使用 ZSET：`ZADD delay_queue timestamp task`，score 为执行时间戳。定时任务用 `ZRANGEBYSCORE delay_queue 0 current_timestamp LIMIT 0 1` 获取到期任务，处理完后 `ZREM` 删除。配合 `BRPOP` 或轮询实现。也可以使用 Redisson 的 `DelayedQueue` 或 Redis Stream 配合过期机制。


---

# Redis 面试题详解（Part 4：题目 41-53）
