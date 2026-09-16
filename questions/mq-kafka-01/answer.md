## 逐环节分析

**生产者**

- `acks=all`（`-1`）：所有 ISR 副本写入成功才返回；
- `retries` 足够大 + `enable.idempotence=true`（幂等生产者，避免重试造成重复）；
- `max.in.flight.requests.per.connection=1` 或配合幂等使用（≤5 时幂等仍有效）。

**Broker**

- `replication.factor ≥ 3`；
- `min.insync.replicas ≥ 2`（与 `acks=all` 配合，保证至少两个副本落盘）；
- `unclean.leader.election.enable=false`：不允许落后副本当 leader，宁可用不可用换一致性。

**消费者**

- 关闭**自动提交**：`enable.auto.commit=false`；
- **处理完成后**再手动提交 offset（至少一次语义）；
- 消费失败不要吞异常，重试 + 死信主题。

## 关键：语义选择

Kafka 只能保证**至少一次**（默认）或**至多一次**。要业务上的"恰好一次"，必须在消费端做**幂等**（唯一键去重表 / 状态机）。
