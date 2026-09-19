## 追问 1：RPC 为什么比 HTTP 快？

三个原因：第一，序列化方式不同，RPC 用二进制序列化（Protobuf/Hessian2），比 JSON 文本序列化体积小 30%-50%，解析也快得多；第二，协议头不同，RPC 协议头很精简（几十字节），HTTP 每次请求都带大量 Header（几百字节）；第三，连接管理，RPC 框架一般用长连接 + 多路复用，HTTP/1.1 默认短连接（即使 keep-alive 也是串行请求）。

## 追问 2：你的项目里用的 HTTP 还是 RPC？

内部服务之间用的 OpenFeign（HTTP REST），因为团队对 Spring Cloud 更熟悉，而且业务场景对性能要求不是特别极致。如果是对性能要求很高的场景（比如高频交易），会考虑用 Dubbo（RPC）。对外暴露的 API 肯定是 HTTP REST，因为要兼容各种客户端。

## 追问 3：gRPC 和 Dubbo 怎么选型？

gRPC 是通用的 RPC 框架，跨语言支持好，适合多语言混合的项目。Dubbo 是 Java 生态的 RPC 框架，跟 Spring 集成更好，服务治理能力更强（负载均衡、熔断限流、链路追踪等开箱即用）。如果团队以 Java 为主，选 Dubbo；如果多语言混合，选 gRPC。
