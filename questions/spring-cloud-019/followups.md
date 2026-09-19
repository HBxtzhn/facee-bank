## 追问 1：Eureka 和 Nacos 的区别？

功能上 Nacos 更丰富，既能做注册中心又能做配置中心。一致性上 Eureka 只支持 AP，Nacos 支持 AP/CP 切换。健康检查上 Nacos 支持 TCP/HTTP/MySQL 等多种方式，Eureka 只有心跳。Nacos 还支持灰度发布、权重路由等高级功能。而且 Eureka 已经停更了。

## 追问 2：Eureka 的增量同步是怎么实现的？

Eureka Client 启动时拉取全量注册表（/apps 接口），之后每30秒调用增量拉取接口（/apps/delta）获取变更。Server 维护一个最近变化的队列（最近注册、更新、注销的实例），增量接口返回这个队列里的变化。Client 收到增量后跟本地缓存合并。如果增量同步失败（hash 校验不一致），Client 会回退到全量拉取。

## 追问 3：如果所有 Eureka Server 都挂了，服务还能调用吗？

可以。Eureka Client 本地缓存了服务注册表（Applications 对象），Server 全挂了，Client 用缓存的服务列表继续调用。只是新注册的服务发现不了，下线的服务也不能及时感知。这就是 AP 模型的设计——保证可用性。
