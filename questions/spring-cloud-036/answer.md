1. **定义**：Hystrix 是 Netflix 开源的熔断器框架，提供服务降级、熔断、线程隔离、请求缓存、请求合并、监控等功能。Spring Cloud 早期用 Hystrix 做熔断降级。

2. **核心功能**：
   - **服务降级（Fallback）**：调用失败时返回兜底数据。
   - **熔断（Circuit Breaker）**：失败率超过阈值自动熔断，防止雪崩。
   - **线程隔离（Bulkhead）**：每个服务调用使用独立的线程池，一个服务挂了不影响其他服务。
   - **请求缓存（Request Cache）**：同一个请求在短时间内多次调用只执行一次。
   - **请求合并（Request Collapsing）**：短时间内多个相似请求合并成一个批量请求。

3. **线程池隔离 vs 信号量隔离**：Hystrix 默认用线程池隔离（每个服务一个线程池），好处是隔离性强，坏处是线程切换开销大。也支持信号量隔离（计数器），开销小但隔离性弱。

4. **现状**：Hystrix 已经停更（最后版本 1.5.18，2018年），Spring Cloud 从 2020 版本开始移除了 Hystrix。替代方案是 Sentinel 或 Resilience4j。

## 扩展知识

- **Hystrix Dashboard**：Hystrix 提供了实时监控面板，可以查看每个命令的调用量、成功/失败/熔断次数、响应时间分布等。配合 Turbine 可以聚合多个服务的监控数据。
- **Hystrix 的问题**：线程池隔离虽然隔离性好，但线程开销大（每个服务一个线程池，服务多了线程数爆炸）。编程模型比较重，需要继承 HystrixCommand。不支持动态规则配置。
- **迁移方案**：从 Hystrix 迁移到 Sentinel 或 Resilience4j。Sentinel 功能更全（限流、熔断、系统保护），Resilience4j 更轻量（函数式编程风格）。
