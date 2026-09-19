1. **为什么需要集群限流**：单机限流只能限制单个实例的流量。比如有10个实例，每个实例限流100 QPS，总流量限制是 1000 QPS。但如果流量不均匀（某个实例分到200 QPS，另一个只有10 QPS），单机限流就不够精确。集群限流可以控制整个集群的总 QPS。

2. **架构**：Sentinel 集群限流采用 Token Server（令牌服务器）模式。Token Server 维护全局的令牌计数，各 Sentinel 客户端（Token Client）在处理请求时向 Token Server 申请令牌。Token Server 判断全局 QPS 是否超过阈值，返回允许或拒绝。

3. **工作流程**：请求到达 Sentinel Client → Client 向 Token Server 发送令牌申请（包含资源名和申请数量）→ Token Server 检查全局计数是否超过阈值 → 返回允许或拒绝 → Client 根据结果放行或拒绝请求。

4. **Token Server 部署模式**：
   - **独立部署**：Token Server 作为独立进程部署，高可用。适合大规模集群。
   - **嵌入模式**：Token Server 嵌入在某个应用实例中。节省资源，但该实例挂了会影响限流。适合小规模集群。

5. **通信方式**：Client 和 Server 之间通过 Netty 长连接通信，性能很好。Client 启动时连接到 Token Server，运行期间维持长连接。

## 扩展知识

- **Token Server 的高可用**：独立部署的 Token Server 可以做主备（通过 Nacos 注册，Client 自动切换）。嵌入模式下，如果 Token Server 实例挂了，Client 会降级到单机限流模式。
- **性能考虑**：集群限流引入了网络通信开销（Client → Token Server），会增加几毫秒的延迟。Token Server 本身也有性能瓶颈，大规模集群下需要优化（比如批量申请令牌、本地缓存等）。
- **配置方式**：通过 Sentinel Dashboard 或 Nacos 配置集群限流规则。规则格式跟单机限流类似，只是多了 clusterMode=true 标记。
