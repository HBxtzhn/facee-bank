## 追问 1：Spring Cloud 和 Dubbo 怎么选型？

如果团队对性能要求高，内部服务调用量大，选 Dubbo（RPC 协议，性能更好）。如果需要完整的微服务治理方案，快速搭建，选 Spring Cloud。实际上很多公司是混用的：Dubbo 做内部 RPC 调用，Spring Cloud Gateway 做对外网关，Nacos 做注册中心，Sentinel 做限流。

## 追问 2：Spring Cloud 最大的坑是什么？

版本兼容问题。Spring Cloud 版本要跟 Spring Boot 版本严格对应，升一个版本可能一堆组件不兼容。另外 Netflix 组件停更导致的迁移成本也很大，从 Eureka + Hystrix 迁移到 Nacos + Sentinel 花了不少时间。

## 追问 3：你觉得 Spring Cloud 适合什么场景？

适合中大型项目，团队有一定规模（10人以上），业务复杂度较高需要完整微服务治理的场景。小项目、小团队不建议用，单体应用 + Spring Boot 就够了，上微服务反而增加运维负担。
