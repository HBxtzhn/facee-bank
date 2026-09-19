1. **定义**：Seata 是阿里开源的分布式事务解决方案（Simple Extensible Autonomous Transaction Architecture），提供一站式分布式事务体验，支持 AT、TCC、Saga、XA 四种事务模式。

2. **核心角色**：TC（Transaction Coordinator，事务协调者）——Seata Server，维护全局和分支事务的状态，驱动全局事务的提交或回滚；TM（Transaction Manager，事务管理器）——业务侧的 @GlobalTransactional 注解入口，负责开启、提交或回滚全局事务；RM（Resource Manager，资源管理器）——管理分支事务所在的资源，向 TC 注册分支事务并汇报状态。

3. **AT 模式**：默认模式，基于一阶段提交 + 二阶段异步补偿。一阶段本地事务直接提交（拿到全局锁），同时记录 undo_log；二阶段如果需要回滚，根据 undo_log 生成反向 SQL 补偿。对业务零侵入。

4. **TCC 模式**：业务层面的两阶段，Try 预留资源，Confirm 确认提交，Cancel 取消释放。性能好但侵入性强，每个接口要写三个方法。

5. **适用场景**：订单创建（扣库存 + 创建订单 + 加积分）、跨行转账、供应链金融等需要跨服务数据一致性的场景。

## 扩展知识

- **全局事务 ID（XID）**：TM 开启全局事务时，TC 生成一个全局唯一的 XID，通过 HTTP Header 或 RPC Context 在微服务之间传递。每个服务拿到 XID 后向 TC 注册分支事务。
- **Seata 版本**：1.x 和 2.x 架构差异较大，2.x 引入了 gRPC 通信替代了 1.x 的 Netty，性能更好。生产环境建议用最新的稳定版。
- **与 Spring Cloud 集成**：引入 spring-cloud-starter-alibaba-seata 依赖，在业务入口加 @GlobalTransactional 注解即可。Feign 调用会自动传递 XID。
