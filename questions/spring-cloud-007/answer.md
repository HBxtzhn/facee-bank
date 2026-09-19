1. **优点 - 生态完善**：Spring Cloud 提供了微服务全套解决方案，从注册中心、配置中心、网关、负载均衡、熔断限流到链路追踪，开箱即用，跟 Spring Boot 无缝集成。

2. **优点 - 社区活跃**：背靠 Spring 生态，社区庞大，文档丰富，遇到问题容易找到解决方案。版本迭代快，持续跟进新技术。

3. **优点 - 开发效率高**：基于 Spring Boot 的自动配置，大量 Starter 依赖，几行配置就能搭建一个微服务。声明式的 Feign 调用、注解驱动的熔断降级，开发体验好。

4. **优点 - 技术选型灵活**：每个组件都有多种实现可选（注册中心可选 Nacos/Eureka/Consul，熔断可选 Sentinel/Hystrix/Resilience4j），可以根据需要替换。

5. **缺点 - 性能一般**：基于 HTTP 协议通信（REST），相比 Dubbo 的自定义 TCP 协议，性能有差距。序列化默认用 JSON，也比 Dubbo 的 Hessian2 慢。

6. **缺点 - 运维复杂**：组件多，部署维护成本高。虽然每个组件都能用，但深度不如专门的中间件。比如限流功能不如专业的 Sentinel 深入。

## 扩展知识

- **性能优化**：可以通过替换通信协议（HTTP/2）、优化序列化（Protobuf 替代 JSON）、连接池调优等方式提升性能。
- **版本管理**：Spring Cloud 的版本号跟 Spring Boot 版本有对应关系，升级时要特别注意兼容性。早期用 release train 命名（如 Greenwich、Hoxton），2020 后改为年月命名（如 2021.0.6）。
- **国内替代方案**：很多公司会混用 Spring Cloud 和 Dubbo，用 Dubbo 做高性能 RPC，用 Spring Cloud 做网关和配置管理。
