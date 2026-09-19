1. **核心原因**：Feign 第一次调用时需要做很多初始化工作——建立 HTTP 连接、加载配置、初始化负载均衡器、创建连接池等，这些操作只在第一次调用时执行，后续调用复用已建立的连接和资源。

2. **具体原因**：
   - **HTTP 连接建立**：TCP 三次握手 + HTTPS 的 TLS 握手（如果用了 HTTPS），第一次需要完整的握手过程。
   - **连接池初始化**：如果使用 OkHttp 或 Apache HttpClient，第一次调用时初始化连接池。
   - **负载均衡器初始化**：第一次调用时从注册中心拉取服务列表，初始化 LoadBalancer。
   - **JDK 动态代理初始化**：Feign 的代理对象在第一次调用时完成一些懒加载的初始化。
   - **DNS 解析**：第一次调用需要 DNS 解析目标服务的域名。

3. **解决方案**：
   - **预热**：项目启动后主动调用一次（@PostConstruct 或 ApplicationRunner），让初始化在启动阶段完成。
   - **连接池配置**：使用 OkHttp/Apache HttpClient 连接池，配置合理的连接超时和读取超时。
   - **懒加载关闭**：Spring Cloud 的 Ribbon/LoadBalancer 默认懒加载，可以关闭：spring.cloud.loadbalancer.lazy-load=false。

4. **生产影响**：第一次调用慢只影响第一个请求，后续请求都是毫秒级。但如果接口响应时间有 SLA 要求，第一个请求超时可能导致熔断，需要注意。

## 扩展知识

- **具体耗时分析**：正常情况第一次调用耗时 1-3 秒（取决于网络和服务端响应速度），后续调用 10-50ms。如果用了 HTTPS，第一次可能更慢（TLS 握手）。
- **Sentinel 的坑**：如果 Feign 集成了 Sentinel，第一次调用慢可能导致 Sentinel 认为超时触发熔断。解决方案是配置 Sentinel 的超时时间或者做预热。
- **Spring Cloud 的懒加载**：Spring Cloud 的 Ribbon/LoadBalancer 默认是懒加载的，第一次调用某个服务时才初始化该服务的 LoadBalancer。可以通过 spring.cloud.loadbalancer.lazy-load=false 关闭懒加载，启动时就初始化所有服务的 LoadBalancer。
