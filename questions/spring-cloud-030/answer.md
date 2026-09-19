1. **定义**：Feign 是一个声明式的 HTTP 客户端，通过接口 + 注解的方式简化 HTTP 调用。写一个接口，加几个注解，就能调用远程服务的 REST API。

2. **核心特点**：声明式——定义接口加 @FeignClient 注解，方法上加 @GetMapping/@PostMapping，框架自动生成代理实现；可插拔——支持自定义编码器（Encoder）、解码器（Decoder）、拦截器（Interceptor）、日志级别等；集成负载均衡——跟 Ribbon/LoadBalancer 集成，自动实现客户端负载均衡。

3. **使用示例**：

``java
@FeignClient(name = "order-service")
public interface OrderClient {
    @GetMapping("/api/order/{id}")
    Order getOrder(@PathVariable("id") Long id);

    @PostMapping("/api/order")
    Order createOrder(@RequestBody OrderDTO orderDTO);
}
``

4. **OpenFeign**：Spring Cloud 使用的 Feign 版本，是社区维护的增强版。原版 Feign 是 Netflix 开发的，已经停更。OpenFeign 增加了 Spring MVC 注解支持、与 Spring Cloud 生态的深度集成。

5. **底层实现**：Feign 通过 JDK 动态代理生成接口的代理对象，调用方法时，根据注解构建 HTTP 请求（URL、Method、Header、Body），通过 HTTP 客户端（默认 URLConnection，可替换为 OkHttp/HttpClient）发送请求，收到响应后用解码器反序列化。

## 扩展知识

- **Feign 的组成**：@FeignClient → FeignClientFactoryBean（创建代理）→ Feign.Builder（构建 Feign 实例）→ Contract（解析注解）→ Encoder/Decoder（编解码）→ Client（HTTP 客户端）→ InvocationHandler（代理调用）。
- **日志级别**：NONE（不记录）、BASIC（只记录请求方法/URL/响应状态码）、HEADERS（BASIC + 请求响应头）、FULL（HEADERS + 请求响应体）。生产环境用 BASIC 或 NONE，调试时用 FULL。
- **性能优化**：默认用 URLConnection（不支持连接池），建议替换为 OkHttp 或 Apache HttpClient，支持连接池和超时配置。
