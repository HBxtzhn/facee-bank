1. **协议层面**：HTTP 是应用层协议，基于文本（HTTP/1.1）或二进制帧（HTTP/2），每次请求都要带完整的 Header，开销较大。RPC（远程过程调用）通常基于自定义 TCP 协议或 HTTP/2，协议更紧凑，序列化效率更高。

2. **调用体验**：HTTP 调用像发 HTTP 请求——构造 URL、设置 Header、解析 JSON 响应。RPC 调用像调用本地方法——定义接口，框架自动生成代理，调用方无感知是远程调用。

3. **性能**：RPC 通常比 HTTP 快。原因：RPC 用二进制序列化（Protobuf/Hessian2），比 JSON 体积小、解析快；RPC 可以复用 TCP 长连接，减少握手开销；RPC 协议头更精简，没有 HTTP 那么多 Header。

4. **跨语言**：HTTP 天然支持跨语言（任何语言都能发 HTTP 请求）。RPC 需要各语言有对应的 SDK 和序列化支持（Dubbo 支持 Java/Go/Python/Node 等）。

5. **典型代表**：HTTP REST（Spring Cloud）、gRPC（Google，基于 HTTP/2 + Protobuf）；RPC 框架（Dubbo、Thrift、gRPC）。

## 概念对比

| 维度 | HTTP REST | RPC（如 Dubbo） |
|------|-----------|----------------|
| 协议 | HTTP/1.1 文本协议 | 自定义TCP/HTTP2 二进制协议 |
| 序列化 | JSON（文本） | Hessian2/Protobuf（二进制） |
| 性能 | 较低 | 较高 |
| 调用方式 | 构造请求、解析响应 | 像调用本地方法 |
| 跨语言 | 天然支持 | 需要多语言SDK |
| 适用场景 | 对外API、跨语言 | 内部高性能调用 |
| 代表框架 | Spring MVC、JAX-RS | Dubbo、gRPC、Thrift |

## 扩展知识

- **gRPC**：Google 开源的 RPC 框架，基于 HTTP/2 + Protobuf。支持双向流、多路复用、头部压缩，性能很好。支持多种语言。Spring Cloud 也在逐步引入 gRPC 支持。
- **Dubbo 的协议**：Dubbo 默认用 Dubbo 协议（自定义 TCP 协议 + Hessian2 序列化），也支持 gRPC、REST、HTTP 等。Dubbo 协议性能比 HTTP REST 高 30%-50%。
- **Spring Cloud 的 HTTP 优化**：可以通过 HTTP/2、连接池（Apache HttpClient）、Protobuf 替代 JSON 等方式优化 HTTP 性能。
