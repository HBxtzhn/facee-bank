## 追问 1：Spring Cloud Config 和 Nacos Config 的区别？

存储不同：Config 用 Git，Nacos 用内置数据库。动态刷新不同：Config 依赖 MQ 广播，Nacos 用长连接直接推送。功能上 Nacos 支持灰度发布、管理界面、权限管理，Config 都不支持。所以国内基本都用 Nacos 替代了。

## 追问 2：Config Server 怎么做高可用？

Config Server 本身是无状态的，前面挂一个负载均衡（Nginx/Eureka 注册）就行。多个 Config Server 实例都从同一个 Git 仓库拉取配置，保证配置一致。客户端通过 Ribbon/LoadBalancer 负载均衡选择 Config Server。

## 追问 3：Git 仓库里配置文件格式是什么？

默认是 YAML 或 Properties 格式。命名规则是 {application}-{profile}.yml，比如 order-service-dev.yml。也支持 {application}.yml 作为默认配置，所有环境共享。
