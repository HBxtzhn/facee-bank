1. **服务注册与发现**：Nacos/Eureka/Consul，让服务能自动注册和被发现。
2. **配置中心**：Nacos Config/Apollo/Spring Cloud Config，集中管理配置文件。
3. **服务调用**：OpenFeign（声明式 HTTP 调用）、RestTemplate + LoadBalancer。
4. **负载均衡**：Spring Cloud LoadBalancer（替代已移除的 Ribbon），客户端负载均衡。
5. **熔断限流降级**：Sentinel/Hystrix（已移除）/Resilience4j，保护服务不被雪崩。
6. **API 网关**：Spring Cloud Gateway（替代已移除的 Zuul），统一入口、路由转发、鉴权限流。
7. **链路追踪**：Micrometer Tracing（替代已移除的 Sleuth）+ Zipkin/SkyWalking。
8. **分布式事务**：Spring Cloud Alibaba Seata。
9. **消息驱动**：Spring Cloud Stream（对接 Kafka/RocketMQ/RabbitMQ）。
10. **安全**：Spring Cloud Security（OAuth2 + JWT 认证授权）。

## 扩展知识

- **版本变化**：Spring Cloud 2020 版本是一个分水岭，移除了 Netflix 系列组件（Eureka、Hystrix、Ribbon、Zuul），替换为社区维护的新组件。
- **Spring Cloud Alibaba**：阿里巴巴提供的 Spring Cloud 增强套件，包含 Nacos（注册/配置）、Sentinel（限流熔断）、Seata（分布式事务）、RocketMQ（消息）等，国内用的最多。
- **Spring Cloud 项目列表**：除了核心组件，还有 Spring Cloud Task（批处理）、Spring Cloud Function（函数式）、Spring Cloud Contract（契约测试）等。
