1. **硬件负载均衡**：F5、A10 等专用硬件设备，性能极高，能处理百万级并发，但价格昂贵。适合大型企业、运营商。

2. **软件负载均衡**：Nginx（最流行）、HAProxy、LVS（Linux Virtual Server，四层负载均衡）、Traefik。免费，性能也不错，大部分公司用这种。

3. **DNS 负载均衡**：通过 DNS 解析到不同的 IP，实现简单的负载均衡。比如 DNS 轮询、GeoDNS（按地理位置解析）。优点是简单，缺点是 DNS 有缓存，更新不及时。

4. **客户端负载均衡**：Ribbon、Spring Cloud LoadBalancer。负载均衡逻辑在客户端（调用方），客户端从注册中心拿到服务列表，在本地选一个实例调用。

5. **云服务负载均衡**：云厂商提供的负载均衡服务（阿里云 SLB/ALB/NLB、AWS ELB/ALB/NLB），开箱即用，自动扩缩容。

## 扩展知识

- **LVS**：Linux 内核层面的四层负载均衡，性能极高（比 Nginx 还高），但只支持四层，配置复杂。一般用 LVS + Nginx 的组合，LVS 做四层分发，Nginx 做七层路由。
- **Nginx 的 upstream 模块**：Nginx 通过 upstream 配置后端服务器列表，支持轮询、加权轮询、IP hash、least_conn 等算法。配合 health_check（商业版）或 max_fails + 
ail_timeout（开源版）实现健康检查。
- **Spring Cloud LoadBalancer**：Spring Cloud 2021+ 替代 Ribbon 的客户端负载均衡器。支持轮询、随机、加权等算法，可以通过自定义 ReactorLoadBalancer 扩展。
