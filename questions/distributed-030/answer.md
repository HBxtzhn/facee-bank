三个环节:
- **生产者**: confirm确认机制，消息投递到broker后回调ack，没收到ack重发
- **Broker**: 持久化配置——exchange和queue声明durable=true，消息delivery_mode=2持久化磁盘
- **消费者**: 手动ack代替自动ack——处理完业务逻辑再ack，处理中宕机消息重新入队
