1. **集成 LoadBalancer**：Feign 本身不做负载均衡，通过集成 Spring Cloud LoadBalancer（早期是 Ribbon）实现。Feign 的 Client 接口被替换为 LoadBalancerFeignClient（或 FeignBlockingLoadBalancerClient），每次请求时从注册中心获取服务列表，通过负载均衡算法选一个实例。

2. **调用流程**：Feign 发起请求 → FeignBlockingLoadBalancerClient.execute() → 从注册中心获取目标服务的所有实例 → ReactorLoadBalancer 选择一个实例（默认轮询）→ 用选中的实例 IP:Port 替换 Feign 请求中的服务名 → 发送 HTTP 请求。

3. **自定义负载均衡算法**：可以实现 ReactorLoadBalancer<ServiceInstance> 接口，自定义选择实例的逻辑。比如加权轮询、随机、按元数据路由等。

4. **重试机制**：Feign 集成了 Retryer，负载均衡选择的实例如果调用失败，可以自动重试其他实例。默认不重试，可以通过配置开启。

## 扩展知识

- **Ribbon 时代的实现**：早期 Feign 集成 Ribbon，通过 RibbonLoadBalancerClient 实现负载均衡。Ribbon 提供了丰富的负载均衡规则（RoundRobinRule、RandomRule、WeightedResponseTimeRule 等）。Spring Cloud 2021+ 移除了 Ribbon，改用 Spring Cloud LoadBalancer。
- **Spring Cloud LoadBalancer 的实现**：基于 Reactor 的响应式编程模型，核心接口是 ReactorLoadBalancer<T>。默认实现是 RoundRobinLoadBalancer（轮询），也支持 RandomLoadBalancer。可以通过 @LoadBalancerClient 注解为特定服务配置自定义的 LoadBalancer。
- **源码关键类**：FeignBlockingLoadBalancerClient → LoadBalancerClientFactory → ReactorLoadBalancer<ServiceInstance> → ServiceInstanceListSupplier（从注册中心获取实例列表）。
