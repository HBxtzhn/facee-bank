## 追问 1：Pub/Sub 消息丢失怎么办？如何保证消息可靠？

**标准答案**：Pub/Sub 本身不保证消息可靠投递。如需可靠消息，应使用 Redis Stream（支持持久化和 ACK）或将消息持久化到外部消息队列（如 Kafka/RocketMQ）。也可以通过业务层实现补偿机制，如消息重试和幂等消费。

## 追问 2：Pub/Sub 在 Redis Cluster 中有什么限制？

**标准答案**：在 Redis Cluster 中，Pub/Sub 的消息广播是全局的，发布者向任意节点发送 `PUBLISH`，消息会被广播到所有节点的所有订阅者。Redis 7.0 引入了 SSUBSCRIBE/SPUBLISH 用于 shard 级别的发布订阅，消息只在负责对应 slot 的节点内广播，减少跨节点通信开销。

## 追问 3：Redis Stream 相比 Pub/Sub 有哪些优势？

**标准答案**：Stream 支持消息持久化（写入 AOF/RDB）、消费者组（多个消费者竞争消费）、消息确认（XACK）、消息回溯（XRANGE）、阻塞读取（XREAD BLOCK），适合需要可靠投递和有序消费的场景。Pub/Sub 适合实时广播但不需要持久化的场景。
