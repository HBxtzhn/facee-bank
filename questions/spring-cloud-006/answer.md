1. **为什么需要**：微服务架构下一个请求可能经过网关、服务A、服务B、服务C 等多个服务，出了问题很难定位是哪个服务、哪个环节出了故障。链路追踪能可视化整个调用链路，快速定位问题。

2. **核心概念**：TraceId（全局唯一追踪ID，贯穿整个调用链）、SpanId（每个服务/操作的唯一标识）、ParentSpanId（父Span，表示调用关系）。

3. **Spring Cloud Sleuth + Zipkin（老方案）**：Sleuth 负责在请求中注入和传递 TraceId/SpanId，自动埋点记录每个服务的耗时；Zipkin 负责收集和展示链路数据，提供 Web UI 查看调用链路图。

4. **Micrometer Tracing + SkyWalking（新方案）**：Spring Cloud 2021+ 移除了 Sleuth，改用 Micrometer Tracing 作为抽象层。SkyWalking 是国产 APM 系统，基于 Java Agent 字节码增强，零侵入，支持链路追踪、性能监控、告警。

5. **跨服务传递 TraceId**：通过 HTTP Header 或 RPC Context 传递。Sleuth 自动在 Feign/RestTemplate 请求头里注入 	raceId 和 spanId，下游服务自动提取。

## 扩展知识

- **SkyWalking 原理**：基于 Java Agent 的字节码增强技术（Instrumentation API），在类加载时修改字节码，自动在方法前后插入埋点代码。不需要改业务代码，甚至不需要改启动参数以外的任何东西。
- **Zipkin 数据流**：应用 → Reporter（HTTP/Kafka）→ Zipkin Collector → Storage（Elasticsearch/MySQL）→ Zipkin UI。
- **日志关联**：链路追踪跟日志系统结合，在日志里打印 TraceId，排查问题时通过 TraceId 就能搜出整个调用链的所有日志。MDC（Mapped Diagnostic Context）实现日志注入。
- **性能影响**：链路追踪会有一定的性能开销（一般 5%-10%），生产环境一般只采样一部分请求（比如 10%），不是每个请求都追踪。

## 概念对比

| 特性 | Sleuth + Zipkin | SkyWalking |
|------|----------------|------------|
| 侵入性 | 需要引入 SDK | Java Agent 零侵入 |
| 语言支持 | 主要 Java | 多语言（Java/Go/Python/Node） |
| 功能 | 链路追踪 | 链路追踪 + 性能监控 + 告警 |
| 存储 | Elasticsearch/MySQL | Elasticsearch/H2/TiDB |
| 社区 | 国外为主 | 国内 Apache 顶级项目 |
| 性能开销 | 中等 | 较低 |
