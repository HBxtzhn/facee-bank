1. **DDD（领域驱动设计）是什么**：一种软件设计方法论，核心思想是以业务领域为中心驱动软件设计，而不是以技术为中心。通过战略设计和战术设计，把复杂的业务逻辑组织成清晰的领域模型。

2. **战略设计**：划分限界上下文（Bounded Context），每个限界上下文对应一个微服务。定义上下文之间的映射关系（共享内核、客户-供应商、防腐层等）。统一语言（Ubiquitous Language），开发和业务用同一套术语。

3. **战术设计**：在限界上下文内部，用实体（Entity）、值对象（Value Object）、聚合（Aggregate）、聚合根（Aggregate Root）、领域服务（Domain Service）、领域事件（Domain Event）、仓储（Repository）等模式构建领域模型。

4. **分层架构**：DDD 推荐四层架构——用户接口层（Interface）、应用层（Application）、领域层（Domain）、基础设施层（Infrastructure）。核心业务逻辑在领域层，不依赖任何外部框架。

5. **跟微服务的关系**：DDD 的限界上下文是微服务拆分的最佳实践指导。一个限界上下文对应一个微服务，上下文之间通过领域事件或 API 通信。

6. **落地难度**：DDD 的理论很完美，但落地难度大。需要团队对业务有深入理解，需要大量的前期建模工作。很多团队只用了 DDD 的部分思想（比如限界上下文拆分服务），没有完全落地战术设计。

## 扩展知识

- **贫血模型 vs 充血模型**：传统三层架构是贫血模型，Service 包含所有业务逻辑，Entity 只是数据载体（getter/setter）。DDD 推崇充血模型，Entity 内部包含业务行为，比如 order.pay() 而不是 orderService.pay(order)。
- **聚合的设计原则**：一个事务只修改一个聚合；聚合之间通过 ID 引用，不直接持有对象引用；跨聚合的操作通过领域事件实现最终一致性。
- **防腐层（ACL）**：在自己的领域模型和外部系统之间加一层隔离，防止外部系统的模型侵入自己的领域。比如调用第三方支付接口，把第三方的 PaymentResponse 转换成自己领域的 PaymentResult。
- **Event Storming（事件风暴）**：一种快速建模的工作坊方法，通过便利贴梳理业务流程中的领域事件、命令、聚合，快速达成团队共识。

## 概念对比

| 概念 | 说明 | 举例 |
|------|------|------|
| 实体（Entity） | 有唯一标识，通过标识区分 | Order（有 orderId） |
| 值对象（Value Object） | 无标识，通过属性值相等判断 | Money（金额+币种） |
| 聚合（Aggregate） | 一组相关对象的集合，有聚合根 | Order + OrderItems |
| 聚合根（Aggregate Root） | 聚合的入口，外部只能通过聚合根访问 | Order 是聚合根 |
| 领域服务（Domain Service） | 不属于任何实体的业务行为 | 转账服务 |
| 领域事件（Domain Event） | 领域中发生的重要事件 | OrderCreatedEvent |
| 仓储（Repository） | 聚合的持久化抽象 | OrderRepository |
