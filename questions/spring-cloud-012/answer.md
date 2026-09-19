1. **整体方案**：ELK（Elasticsearch + Logstash + Kibana）或 EFK（Elasticsearch + Fluentd/Filebeat + Kibana）是最主流的日志收集方案。微服务场景下，每个服务的日志通过 Filebeat 采集，发送到 Kafka 做缓冲，再由 Logstash 消费写入 Elasticsearch，最后通过 Kibana 查询和展示。

2. **日志格式**：统一用 JSON 格式输出日志，方便 Logstash 解析。日志里必须包含 TraceId（链路追踪ID）、服务名、环境、时间戳等关键字段。

3. **日志规范**：不同级别的日志有不同的用途——ERROR 记录异常和错误，WARN 记录潜在问题，INFO 记录关键业务流程，DEBUG 记录调试信息。生产环境一般只开 INFO 及以上。

4. **日志关联**：通过 MDC（Mapped Diagnostic Context）在日志里自动注入 TraceId，这样通过 TraceId 就能搜出整个调用链的所有日志，快速定位问题。

5. **日志存储策略**：Elasticsearch 按天建索引，设置保留天数（比如30天），过期自动删除。热数据用 SSD，冷数据用 HDD 或者归档到对象存储。

## 扩展知识

- **完整链路**：应用日志 → Logback/Log4j2 写文件 → Filebeat 采集 → Kafka 缓冲 → Logstash 过滤/格式化 → Elasticsearch 存储 → Kibana 查询展示。
- **性能考虑**：日志写入不能阻塞业务线程，用异步 Appender（AsyncAppender）；Filebeat 对资源占用很小，部署在每个节点上；Kafka 做缓冲防止日志洪峰打垮 Elasticsearch。
- **告警**：配合 ElastAlert 或 Kibana Alerting，对 ERROR 日志设置告警规则，出现异常自动通知开发人员。
- **生产踩坑**：日志量太大导致 Elasticsearch 写入瓶颈，解决方案是增加 Kafka 缓冲层、合理设置索引分片数、对非关键字段设置 
ot_analyzed。
