1. **协议不同**：Feign 基于 HTTP 协议（REST API），传输的是文本数据（JSON）。Dubbo 默认使用自定义的 Dubbo 协议（基于 TCP 的二进制协议），传输的是二进制数据（Hessian2 序列化）。

2. **性能不同**：Dubbo 性能远高于 Feign。Dubbo 用二进制序列化，数据体积小，解析快；TCP 长连接复用，减少握手开销。Feign 用 JSON 文本格式，数据体积大，HTTP Header 开销大。性能测试 Dubbo 比 Feign 快 30%-50%。

3. **调用体验不同**：Feign 是声明式 HTTP 客户端，调用时还是 HTTP 请求的感觉（构造 URL、参数、解析响应）。Dubbo 是 RPC 框架，调用时像调用本地方法一样，完全屏蔽了远程调用的细节。

4. **跨语言支持**：Feign 基于 HTTP，天然支持跨语言（任何语言都能发 HTTP 请求）。Dubbo 需要各语言有对应的 SDK（目前支持 Java、Go、Python、Node.js、Rust 等）。

5. **服务治理**：Dubbo 自带丰富的服务治理能力（负载均衡、熔断限流、链路追踪、灰度发布等）。Feign 本身功能简单，需要配合 Spring Cloud 的其他组件（LoadBalancer、Sentinel、Gateway 等）才能实现完整的服务治理。

## 概念对比

| 维度 | Feign | Dubbo |
|------|-------|-------|
| 协议 | HTTP/1.1 | Dubbo协议(TCP)/gRPC |
| 序列化 | JSON | Hessian2/Protobuf |
| 性能 | 较低 | 高 |
| 调用方式 | 声明式HTTP | RPC（像本地调用） |
| 跨语言 | 天然支持 | 需要多语言SDK |
| 服务治理 | 需要配合Spring Cloud组件 | 内置丰富功能 |
| 适用场景 | 对外API、跨语言 | 内部高性能调用 |

## 扩展知识

- **Dubbo 3.x 的变化**：Dubbo 3.0 引入了 Triple 协议（基于 gRPC/HTTP/2），兼容了 HTTP 生态，同时保持了 RPC 的高性能。Dubbo 3.x 也支持了 Service Mesh 场景。
- **混合使用**：很多公司会同时用 Feign 和 Dubbo——内部核心服务之间用 Dubbo（高性能），对外暴露 API 用 Feign/Spring MVC（HTTP），网关用 Spring Cloud Gateway。
- **Spring Cloud 集成 Dubbo**：可以通过 spring-cloud-starter-dubbo 把 Dubbo 服务注册到 Nacos，跟 Spring Cloud 生态融合。
