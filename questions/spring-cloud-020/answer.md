1. **服务注册原理**：Eureka Client 启动时，通过 REST 调用 Eureka Server 的 /eureka/apps/{appName} 接口，把自己的实例信息（InstanceInfo）注册上去。Server 收到后存入内存中的 ConcurrentHashMap（两级 Map：appName → Map<instanceId, InstanceInfo>）。

2. **心跳续约原理**：Client 注册成功后，每隔30秒（可配置）向 Server 发送心跳（PUT /eureka/apps/{appName}/{instanceId}）。Server 收到心跳后更新实例的最后心跳时间。如果超过90秒（默认3倍心跳间隔）没收到心跳，Server 就把这个实例剔除。

3. **服务发现原理**：Client 启动时全量拉取注册表（GET /eureka/apps），之后每30秒增量拉取（GET /eureka/apps/delta）。拉取的数据缓存在客户端本地，调用时直接从本地缓存获取服务列表。

4. **节点间同步原理**：Eureka Server 集群中，每个节点收到注册/注销请求后，除了处理本地请求，还会把请求转发给其他节点（peer replication）。通过 
eplicationToPeers() 方法实现。节点之间是异步复制，允许短暂不一致。

5. **雪崩保护原理**：Server 每分钟统计一次心跳成功率（实际收到心跳数/预期收到心跳数）。如果低于85%（阈值可配置），进入保护模式——不剔除任何超时实例。网络恢复后，心跳成功率回升，自动退出保护模式。

## 扩展知识

- **内存存储结构**：Registry 是一个 ConcurrentHashMap<String, Map<String, Lease<InstanceInfo>>>，key 是应用名，value 是该应用下所有实例的租约（Lease）。Lease 里包含 InstanceInfo（实例信息）和 lastUpdateTimestamp（最后更新时间）。
- **增量同步的实现**：Server 维护一个 
ecentlyChangedQueue（最近变更队列），记录最近15分钟内的所有注册/更新/注销事件。增量接口返回这个队列的内容。Client 收到后跟本地缓存合并，并通过 
egionsHash 校验一致性，不一致则全量拉取。
- **自我保护阈值的计算**：expectedNumberOfClientsSendingRenews = 注册实例数 * (60/心跳间隔秒数)。阈值 = expectedNumberOfClientsSendingRenews * 0.85。如果每分钟实际收到的心跳数低于阈值，触发保护。
