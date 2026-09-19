## 追问 1：为什么 Eureka 选 AP，Zookeeper 选 CP？

设计目标不同。Eureka 就是为微服务注册发现设计的，微服务场景下可用性最重要，网络分区时宁可返回旧的服务列表也不能让服务不可用。Zookeeper 是通用的分布式协调服务，很多场景（Leader选举、分布式锁）要求强一致，所以选了 CP。

## 追问 2：Nacos 的 AP 和 CP 模式具体怎么切换？

不需要手动切换，通过注册实例时的 ephemeral 参数控制。ephemeral=true（默认）注册为临时实例，用 Distro 协议（AP）；ephemeral=false 注册为持久实例，用 Raft 协议（CP）。临时实例客户端心跳维持，允许短暂不一致；持久实例服务端主动探测，强一致。

## 追问 3：为什么选 Nacos 不用 Zookeeper？

第一，Nacos 既能做注册中心又能做配置中心，少维护一个组件；第二，Nacos 默认 AP 模式，微服务场景下可用性更好；第三，Nacos 支持灰度发布、权重路由等高级功能；第四，Nacos 中文文档好，社区活跃，出问题好排查。Zookeeper 更适合 Dubbo 生态。
