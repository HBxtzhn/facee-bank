1. **定义**：Consul 是 HashiCorp 公司开源的服务发现和配置管理工具，用 Go 语言编写。提供服务注册发现、健康检查、KV 存储、多数据中心支持等功能。

2. **核心功能**：服务注册发现——服务注册到 Consul，消费者通过 DNS 或 HTTP API 发现服务；健康检查——支持 Script、HTTP、TCP、TTL 等多种健康检查方式；KV 存储——可以当配置中心用；多数据中心——原生支持多数据中心部署。

3. **CP 模型**：Consul 使用 Raft 协议保证强一致性，所有节点的数据是一致的。但 Leader 选举期间不可用。

4. **健康检查**：比 Eureka 更灵活，支持多种检查方式——HTTP GET（请求某个URL返回200）、TCP（端口是否通）、Script（执行脚本返回0）、TTL（客户端定时汇报存活状态）。

5. **适用场景**：对一致性要求高的场景、多数据中心部署的场景、基础设施即代码（IaC）的场景。Consul 跟 Terraform、Vault 等 HashiCorp 生态工具配合很好。

## 扩展知识

- **DNS 接口**：Consul 内置 DNS 服务器，可以通过 DNS 查询服务地址（dig @localhost -p 8600 order-service.service.consul），对非 HTTP 协议（比如数据库连接）的服务发现很方便。
- **Connect（服务网格）**：Consul 1.2+ 引入了 Connect 功能，提供服务网格能力，自动为服务间通信建立 TLS 加密通道，支持 Intentions（访问控制策略）。
- **跟 Nacos 的对比**：Nacos 在国内用的更多（中文文档好、阿里生态），Consul 在国外用的更多。Nacos 支持 AP/CP 切换，Consul 只支持 CP。Nacos 的配置中心功能更完善。
