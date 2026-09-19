1. **服务注册与发现**：Nacos、Eureka、Consul、Zookeeper，负责服务的自动注册和发现，让服务之间能互相找到对方。

2. **配置中心**：Nacos Config、Spring Cloud Config、Apollo，集中管理各环境的配置文件，支持动态刷新，不用重启服务。

3. **负载均衡**：Ribbon / Spring Cloud LoadBalancer，客户端负载均衡，从注册中心拿到服务列表后，按算法选一个实例调用。

4. **服务调用**：RestTemplate + LoadBalancer、OpenFeign，声明式的服务调用，写个接口加个注解就能调用远程服务。

5. **熔断降级限流**：Sentinel、Hystrix、Resilience4j，防止服务雪崩，当下游服务不可用时快速失败或者返回兜底数据。

6. **网关**：Spring Cloud Gateway、Zuul，统一入口，做路由转发、鉴权、限流、日志。

7. **链路追踪**：Sleuth + Zipkin / Micrometer Tracing + SkyWalking，追踪一个请求在微服务之间的调用链路，方便排查问题。

## 扩展知识

- **Spring Cloud 版本迭代**：早期用 Netflix 套件（Eureka + Hystrix + Ribbon + Zuul），现在主流是阿里巴巴的 Nacos + Sentinel + Spring Cloud Gateway + OpenFeign 这套组合。
- **Spring Cloud 2020+ 变化**：移除了 Hystrix、Ribbon，推荐用 Resilience4j 和 Spring Cloud LoadBalancer 替代。
- **实际项目选型**：大部分国内公司用 Nacos 做注册中心和配置中心，Gateway 做网关，Sentinel 做限流熔断，OpenFeign 做服务调用，SkyWalking 做链路追踪。

## 概念对比

| 功能 | Netflix 系（老） | 主流替代方案 |
|------|------------------|-------------|
| 注册中心 | Eureka | Nacos / Consul |
| 配置中心 | Spring Cloud Config | Nacos Config / Apollo |
| 负载均衡 | Ribbon | Spring Cloud LoadBalancer |
| 熔断器 | Hystrix | Sentinel / Resilience4j |
| 网关 | Zuul | Spring Cloud Gateway |
| 服务调用 | Feign | OpenFeign |
