## 追问 1：为什么选 Spring Cloud Gateway 不选 Kong？

实际项目是纯 Spring Cloud 体系，用 Spring Cloud Gateway 集成最方便——自动从 Nacos 获取路由规则，集成 Sentinel 做限流，集成 Spring Security 做鉴权。Kong 功能更丰富但需要额外维护，而且跟 Spring Cloud 的集成不如 Gateway 紧密。对于 Spring Cloud 项目，Gateway 是最佳选择。

## 追问 2：Spring Cloud Gateway 的性能怎么样？

很好。基于 Netty + WebFlux，异步非阻塞模型。官方测试单机可以支持几万 QPS（取决于路由规则和过滤器复杂度）。比 Zuul 1.x 高很多（Zuul 基于 Servlet，同步阻塞，线程模型限制了并发能力）。生产环境做集群部署，前面挂负载均衡，可以支撑大流量。

## 追问 3：网关怎么做动态路由？

Spring Cloud Gateway 支持从注册中心（Nacos/Eureka）自动获取服务列表，动态生成路由规则。通过 spring.cloud.gateway.discovery.locator.enabled=true 开启，网关自动为注册中心的每个服务创建路由。也可以自定义 RouteDefinitionLocator，从数据库或配置中心读取路由规则，支持动态修改不需要重启。
