## 追问 1：Spring Cloud 和 Spring Cloud Alibaba 是什么关系？

Spring Cloud 是微服务的标准和规范（定义了一套接口和抽象），Spring Cloud Alibaba 是阿里巴巴基于这套规范的具体实现。比如 Spring Cloud 定义了服务发现的接口，Nacos 就是具体实现之一。Spring Cloud Alibaba 还额外提供了一些阿里特有的组件（Sentinel、Seata 等）。

## 追问 2：你的项目里用了哪些 Spring Cloud 组件？

Nacos 做注册中心和配置中心，OpenFeign 做服务调用，Spring Cloud LoadBalancer 做负载均衡，Sentinel 做限流熔断，Gateway 做网关，SkyWalking 做链路追踪，Seata 做分布式事务。基本上是国内主流的全家桶。

## 追问 3：Spring Cloud 组件之间的依赖关系是怎样的？

服务注册发现是基础，其他组件都依赖它。配置中心独立运作。服务调用（Feign）依赖注册发现获取服务列表，依赖 LoadBalancer 做负载均衡。熔断器（Sentinel）嵌入在 Feign 调用链路中。网关（Gateway）依赖注册发现做动态路由。链路追踪贯穿所有组件。
