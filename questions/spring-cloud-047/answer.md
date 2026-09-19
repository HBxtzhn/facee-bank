1. **Spring Cloud Gateway**：Spring 官方的网关，基于 WebFlux（Netty + Reactor），异步非阻塞，性能优秀。支持动态路由、限流、熔断、鉴权等。Spring Cloud 推荐的网关方案。

2. **Zuul 1.x（已淘汰）**：Netflix 开源，基于 Servlet，同步阻塞模型，性能一般。Spring Cloud 早期用 Zuul 做网关，2020 版本开始移除。不推荐新项目使用。

3. **Zuul 2.x**：Netflix 内部使用，基于 Netty，异步非阻塞。但没有开源 Spring Cloud 集成，社区支持少。

4. **Kong**：基于 Nginx 的 API 网关，功能丰富（插件生态），支持 RESTful API 管理、OAuth2、限流等。适合需要完整 API 管理能力的场景。

5. **APISIX**：国产高性能 API 网关，基于 Nginx + Lua，支持动态路由、插件化、多协议（HTTP/gRPC/WebSocket）。性能优秀，社区活跃。

6. **Envoy**：Lyft 开源的高性能代理，Istio 服务网格的默认数据平面。也可以单独做 API 网关，性能极高，但配置复杂。

## 概念对比

| 网关 | 基础 | 性能 | Spring Cloud集成 | 适用场景 |
|------|------|------|-----------------|----------|
| Spring Cloud Gateway | Netty/WebFlux | 高 | 原生支持 | Spring Cloud项目首选 |
| Zuul 1.x | Servlet | 低 | 已移除 | 不推荐 |
| Kong | Nginx | 高 | 需要插件 | API管理平台 |
| APISIX | Nginx+Lua | 高 | 需要适配 | 高性能API网关 |
| Envoy | C++ | 极高 | 需要适配 | 服务网格/高性能 |

## 扩展知识

- **Spring Cloud Gateway 的优势**：跟 Spring Cloud 生态无缝集成（注册发现、配置中心、熔断限流）；支持动态路由（从注册中心自动获取路由规则）；支持 Predicate（路由条件）和 Filter（过滤器）的灵活组合；基于 WebFlux，非阻塞性能优秀。
- **选型建议**：Spring Cloud 项目首选 Spring Cloud Gateway。如果需要更强大的 API 管理能力（API 文档、开发者门户），可以考虑 Kong 或 APISIX。如果已经用了 Istio 服务网格，可以用 Envoy 做网关。
