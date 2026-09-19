1. **定义**：Zuul 是 Netflix 开源的 API 网关，Spring Cloud 早期默认的网关方案。基于 Servlet 容器（Tomcat/Jetty），同步阻塞模型。主要功能是路由转发和过滤器。

2. **核心功能**：
   - **路由转发**：根据 URL 路径规则，把请求转发到对应的后端服务。比如 /api/order/** 转发到订单服务。
   - **过滤器（Filter）**：Zuul 有四种过滤器——pre（路由前）、route（路由时）、post（路由后）、error（错误时）。可以在过滤器里做鉴权、日志、限流等。
   - **负载均衡**：集成 Ribbon，路由时自动做客户端负载均衡。

3. **缺点**：
   - **同步阻塞模型**：基于 Servlet，每个请求占用一个线程，并发能力受限。
   - **性能一般**：线程模型限制了吞吐量，高并发场景下不如异步非阻塞的 Gateway。
   - **已停更**：Netflix 已经停止维护 Zuul 1.x，Spring Cloud 2020 版本移除了 Zuul。

4. **Zuul 2.x**：Netflix 内部开发了 Zuul 2.x（基于 Netty，异步非阻塞），但没有开源 Spring Cloud 集成，社区基本不用。

## 扩展知识

- **Zuul 的线程模型**：每个请求分配一个 Servlet 线程，线程在处理请求期间被阻塞（等待后端服务响应）。高并发时需要大量线程，线程切换开销大。而 Gateway 基于 Netty 的 EventLoop，少量线程就能处理大量并发连接。
- **迁移到 Gateway**：Spring Cloud 项目从 Zuul 迁移到 Gateway，主要改动：过滤器从 ZuulFilter 改为 GatewayFilter/GlobalFilter；路由配置从 zuul.routes 改为 spring.cloud.gateway.routes；Zuul 的同步模型改为 Gateway 的异步模型。
