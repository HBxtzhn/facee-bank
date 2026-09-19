## 追问 1：Dubbo 和 Spring Cloud Gateway 能一起用吗？

当然可以，而且很多公司就是这么用的。架构是：客户端 → Gateway（HTTP 入口）→ 后端服务（Spring MVC 接收 HTTP 请求）→ Dubbo（服务间 RPC 调用）。Gateway 处理外部流量，Dubbo 处理内部服务间通信，各司其职。

## 追问 2：Dubbo 有网关功能吗？

Dubbo 本身没有网关功能。Dubbo 是 RPC 框架，专注于服务间的高性能调用。如果需要网关，应该用专门的网关组件（Spring Cloud Gateway、Kong 等）。Dubbo 3.x 支持了 Triple 协议（基于 gRPC），可以通过 gRPC-Gateway 暴露 HTTP 接口，但这不是完整的网关功能。

## 追问 3：你的项目的架构是怎样的？

架构是：外部请求 → Nginx（负载均衡）→ Spring Cloud Gateway（API 网关，做路由、鉴权、限流）→ 后端微服务（Spring MVC 接收 HTTP 请求）→ 服务间调用用 OpenFeign（HTTP）。如果未来有高性能需求，服务间调用会考虑换成 Dubbo。
