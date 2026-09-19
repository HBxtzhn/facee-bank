1. **定义**：Spring Cloud Gateway 是 Spring 官方的 API 网关，替代 Zuul 1.x。基于 Spring WebFlux（Netty + Reactor）构建，异步非阻塞模型，性能优秀。

2. **核心概念**：
   - **Route（路由）**：网关的基本构建块，由 ID、目标 URI、Predicate 集合和 Filter 集合组成。
   - **Predicate（断言/谓词）**：路由匹配条件，决定请求是否匹配这条路由。比如 Path=/api/order/**（路径匹配）、Header=X-Request-Id（请求头匹配）、After=2024-01-01（时间匹配）。
   - **Filter（过滤器）**：请求/响应的处理逻辑，可以在路由前或路由后执行。分 GatewayFilter（针对单条路由）和 GlobalFilter（针对所有路由）。

3. **工作流程**：请求到达 → 匹配 Predicate（找到匹配的路由）→ 执行 pre 类型的 GatewayFilter → 转发请求到目标服务 → 执行 post 类型的 GatewayFilter → 返回响应。

4. **核心功能**：动态路由（从注册中心自动获取）、限流（集成 Sentinel/Redis）、熔断（集成 Resilience4j）、鉴权（集成 Spring Security）、跨域处理、请求/响应改写、灰度路由。

## 扩展知识

- **配置示例**：

``yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/order/**
          filters:
            - StripPrefix=1
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 10
                redis-rate-limiter.burstCapacity: 20
``

- **动态路由实现**：实现 RouteDefinitionLocator 接口，从 Nacos/数据库读取路由配置。配置变更时发布 RefreshRoutesEvent 事件，Gateway 自动刷新路由。
- **性能优化**：Gateway 基于 Netty，默认 EventLoop 线程数等于 CPU 核心数。可以通过 
eactor.netty.ioWorkerCount 调整。高并发场景下关闭不必要的日志和过滤器。
