1. **Spring Cloud 生态原生支持**：Gateway 是 Spring 官方的网关，跟 Spring Cloud 的其他组件（Nacos、Sentinel、OpenFeign）无缝集成。从 Nacos 自动获取路由规则，集成 Sentinel 做限流，集成 Spring Security 做鉴权，开箱即用。

2. **性能优秀**：基于 Netty + WebFlux，异步非阻塞模型。比 Zuul 1.x 高很多，单机支持几万 QPS。一般项目流量不算特别大，Gateway 完全够用。

3. **功能丰富**：支持动态路由、Predicate 灵活匹配、Filter 链式处理、限流、熔断、鉴权、跨域等。通过 Filter 可以扩展自定义逻辑。

4. **社区活跃**：Spring 官方维护，文档完善，社区活跃，遇到问题容易找到解决方案。

5. **团队技术栈匹配**：团队对 Spring 生态很熟悉，用 Gateway 学习成本低。如果用 Kong 或 APISIX，需要额外学习 Lua 配置和新的技术栈。

## 扩展知识

- **Gateway 的不足**：Gateway 的限流功能依赖 Redis 或 Sentinel，不如 Kong/APISIX 内置的限流功能丰富。Gateway 的 API 管理能力（文档、版本管理、开发者门户）不如专业的 API 网关。但对于微服务内部网关，Gateway 是最佳选择。
- **与其他方案的对比**：如果项目不是 Spring Cloud 体系（比如 Go 微服务），可以考虑 Kong 或 APISIX。如果需要完整的 API 管理平台（对外开放 API），Kong 更合适。如果只是微服务内部网关，Gateway 足够。
