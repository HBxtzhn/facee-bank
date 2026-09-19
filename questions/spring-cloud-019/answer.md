1. **定义**：Eureka 是 Netflix 开源的服务注册与发现组件，Spring Cloud 早期默认的注册中心。分为 Eureka Server（注册中心服务端）和 Eureka Client（客户端）。

2. **核心功能**：服务注册——服务启动时向 Eureka Server 注册自己的信息（IP、端口、服务名等）；服务发现——服务消费者从 Eureka Server 获取服务列表；心跳维持——客户端定时发送心跳（默认30秒），Server 超过一定时间没收到心跳就剔除实例。

3. **高可用设计**：Eureka Server 节点之间通过点对点复制（peer-to-peer replication）同步数据，所有节点都是对等的，没有主从之分。客户端可以连接任意一个节点。

4. **AP 模型**：Eureka 保证高可用（AP），不保证强一致。节点之间数据同步是异步的，可能出现短暂的数据不一致。但微服务场景下这是可以接受的。

5. **雪崩保护**：当心跳失败比例超过阈值（15分钟内低于85%），Eureka 进入保护模式，不会剔除任何实例，防止因为网络问题导致服务列表被清空。

6. **现状**：Netflix 已经停止维护 Eureka 2.0，Spring Cloud 也从 2020 版本开始移除了 Eureka 的 starter。新项目不建议使用。

## 扩展知识

- **Eureka Client 的二级缓存**：客户端启动时从 Server 拉取全量注册表，之后每30秒增量更新。客户端本地有缓存（Applications 对象），即使 Server 全挂了也能用缓存的服务列表调用。
- **Region 和 Zone**：Eureka 支持多数据中心部署，通过 Region 和 Zone 的概念实现就近注册和发现。客户端优先注册和发现同 Zone 的服务实例。
- **为什么被淘汰**：功能单一（只能做注册中心，不能做配置中心）；Nacos 功能更强且兼容 Eureka 的 API；社区停止维护。
