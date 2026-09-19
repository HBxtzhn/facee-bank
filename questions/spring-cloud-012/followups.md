## 追问 1：日志里 TraceId 是怎么注入的？

通过 MDC（Mapped Diagnostic Context）。在请求入口（Servlet Filter 或 Spring Interceptor）里，从请求头提取 TraceId 放到 MDC 里：MDC.put("traceId", traceId)。Logback 的 pattern 里配置 %X{traceId} 就能自动输出。Feign 调用时 TraceId 会通过 HTTP Header 传递到下游服务。

## 追问 2：日志量太大怎么办？

几个方案：第一，合理设置日志级别，生产环境不开 DEBUG；第二，用异步日志（Logback 的 AsyncAppender 或 Log4j2 的 AsyncLogger）；第三，Kafka 做缓冲削峰；第四，Elasticsearch 合理分片，按天建索引，设置生命周期策略自动清理旧数据；第五，非关键日志可以降级，只记录摘要不记录详情。

## 追问 3：线上排查问题一般怎么做的？

首先通过链路追踪系统（SkyWalking）定位到是哪个服务、哪个环节出了问题，拿到 TraceId；然后去 Kibana 里搜这个 TraceId 的所有日志，分析具体的错误信息；如果是性能问题，看 SkyWalking 的调用耗时分析，找到慢 SQL 或慢接口；如果是偶发问题，结合监控指标（Grafana）看那个时间点的 CPU、内存、GC 情况。
