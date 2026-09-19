## 追问 1：Nacos 配置中心怎么保证高可用的？

三个层面：第一，Nacos Server 集群部署，至少3个节点，配置数据在节点间通过 Distro 协议同步；第二，后端存储用 MySQL 主从集群；第三，客户端有本地快照容灾，Server 全挂了也能用本地缓存的配置启动。

## 追问 2：配置变更是怎么通知到所有客户端的？

Nacos 2.x 里，每个客户端跟服务端维持一个 gRPC 长连接。配置变更时，服务端在数据库更新完成后，通过事件机制（EventPublisher）发布配置变更事件，然后遍历所有订阅了该配置的 gRPC 连接，主动推送变更通知。客户端收到通知后重新拉取最新配置。

## 追问 3：Nacos 的配置跟 Spring Cloud Config 有什么区别？

最大的区别是存储和推送机制。Spring Cloud Config 配置存在 Git 里，变更通知依赖 Spring Cloud Bus（MQ 广播），有秒级延迟。Nacos Config 配置存在自己的数据库里，变更通知通过 gRPC 长连接直接推送，毫秒级延迟。另外 Nacos 原生支持灰度发布和配置加密，Spring Cloud Config 不支持。
