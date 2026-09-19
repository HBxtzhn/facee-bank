1. **一致性模型**：Eureka 是 AP（高可用）；Zookeeper 是 CP（强一致）；Nacos 支持 AP/CP 切换（默认 AP）；Consul 是 CP（强一致）。

2. **健康检查**：Eureka 用心跳；Zookeeper 用 KeepAlive 会话机制；Nacos 支持心跳/TCP/HTTP/MySQL 等多种方式；Consul 支持 HTTP/TCP/Script/TTL。

3. **配置中心**：Eureka 不支持；Zookeeper 有 KV 存储可以当配置中心；Nacos 原生支持完善的配置中心功能；Consul 有 KV 存储可以当配置中心。

4. **访问接口**：Eureka 用 HTTP REST；Zookeeper 用 ZooKeeper 协议（基于 TCP 的自定义协议）；Nacos 支持 HTTP 和 gRPC；Consul 支持 HTTP 和 DNS。

5. **社区和生态**：Eureka 已停更；Zookeeper 活跃但偏传统；Nacos 国内最活跃，阿里生态；Consul 国外活跃，HashiCorp 生态。

## 概念对比

| 特性 | Eureka | Zookeeper | Nacos | Consul |
|------|--------|-----------|-------|--------|
| 一致性 | AP | CP | AP/CP | CP |
| 健康检查 | 心跳 | KeepAlive | 心跳/TCP/HTTP | HTTP/TCP/Script/TTL |
| 配置中心 | 不支持 | KV存储 | 完善支持 | KV存储 |
| 协议 | HTTP | 自定义TCP | HTTP/gRPC | HTTP/DNS |
| 雪崩保护 | 有 | 无 | 有 | 无 |
| 多数据中心 | 不支持 | 不支持 | 支持 | 支持 |
| 社区 | 停更 | 活跃 | 活跃 | 活跃 |
| 语言 | Java | Java | Java | Go |

## 扩展知识

- **AP vs CP 怎么选**：微服务注册发现场景选 AP（宁可拿到旧数据也不能拿不到），分布式锁/Leader选举场景选 CP（必须强一致）。
- **Zookeeper 的 Watch 机制**：客户端可以监听节点变化（创建、删除、数据变更），Zookeeper 主动通知客户端。这个机制可以用来实现配置中心和服务发现。
- **Nacos 的 AP/CP 切换**：临时实例（默认）用 Distro 协议（AP），持久实例用 Raft 协议（CP）。通过 ephemeral 参数控制。
