## 追问 1：你的项目里 Feign 和 Dubbo 怎么选的？

内部服务之间用的 Feign，因为团队对 Spring Cloud 更熟悉，而且业务对性能要求不是特别极致。如果未来有高并发场景需要优化性能，会考虑引入 Dubbo。对外暴露的 API 用 Spring MVC + Feign。

## 追问 2：Feign 的性能瓶颈在哪里？怎么优化？

瓶颈主要在三个方面：JSON 序列化/反序列化（可以用 Protobuf 替代）、HTTP Header 开销（可以减少不必要的 Header）、连接管理（用 OkHttp 连接池替代默认的 URLConnection）。另外，HTTP/2 的多路复用也能提升性能。

## 追问 3：Dubbo 能替代 Spring Cloud 吗？

不能完全替代。Dubbo 主要解决的是 RPC 调用和服务治理的问题，Spring Cloud 包含的范围更广（网关、配置中心、链路追踪等）。很多公司是混用的：Dubbo 做内部 RPC，Spring Cloud 的 Gateway 做网关，Nacos 做注册中心和配置中心，Sentinel 做限流。
