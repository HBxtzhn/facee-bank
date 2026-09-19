1. **注册流程**：服务启动时，@EnableDiscoveryClient 注解触发自动配置，ServiceRegistryAutoConfiguration 创建 ServiceRegistry 实例。调用 ServiceRegistry.register() 方法，向注册中心发送注册请求，传入服务名、IP、端口、元数据等信息。

2. **心跳续约**：注册成功后，客户端定时发送心跳（Nacos 默认5秒，Eureka 默认30秒），告诉注册中心"我还活着"。如果长时间没收到心跳，注册中心会剔除该实例。

3. **服务注销**：服务关闭时（Spring 容器关闭），触发 AbstractAutoServiceRegistration 的 destroy() 方法，调用 ServiceRegistry.deregister() 向注册中心发送注销请求。

4. **元数据**：注册时可以携带元数据（Map），比如版本号、环境、权重等。消费者可以根据元数据做自定义路由（比如灰度发布时只调用特定版本的实例）。

5. **Nacos 实现细节**：Nacos Client 通过 gRPC 长连接注册实例，注册信息包含 IP、端口、服务名、分组名、命名空间、元数据、是否临时实例等。临时实例用心跳维持，持久实例由服务端主动探测。

## 扩展知识

- **自动配置源码**：@EnableDiscoveryClient → 导入 EnableDiscoveryClientImportSelector → 根据 spring.cloud.discovery.client.simple.enabled 等配置决定使用哪个注册中心实现。
- **优雅下线**：Spring Cloud 2021+ 支持优雅下线，服务关闭时先从注册中心注销，等待一段时间（spring.cloud.nacos.discovery.heartbeat.timeout）让已有的请求处理完毕，再关闭进程。
- **多网卡问题**：如果服务器有多张网卡，需要指定注册哪个 IP。Nacos 通过 spring.cloud.nacos.discovery.ip 配置，Eureka 通过 eureka.instance.ip-address 配置。
