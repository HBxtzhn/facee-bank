## 追问 1：你的项目用的哪些组件？

项目里用的 Nacos 做注册中心和配置中心，Gateway 做网关，OpenFeign 做服务间调用，Sentinel 做限流和熔断降级，SkyWalking 做链路追踪，Seata 处理分布式事务。这套组合在国内是主流，社区活跃，文档也全。

## 追问 2：为什么不用 Netflix 那套了？

Netflix 那套组件进入维护模式了，Eureka 2.0 开源被砍掉，Hystrix 也停更了。Spring Cloud 官方从 2020 版本开始移除了这些组件。而且 Nacos 相比 Eureka 功能更强，既能做注册中心又能做配置中心，还支持 AP/CP 模式切换。

## 追问 3：Spring Cloud 和 Dubbo 怎么选型？

Spring Cloud 是一套完整的微服务解决方案，覆盖的面广，但每个功能不是最深的。Dubbo 专注 RPC 调用，性能更高，协议更灵活。很多公司会混用，比如用 Dubbo 做内部高性能 RPC 调用，用 Spring Cloud Gateway 做对外网关，用 Nacos 做注册中心。
