| 方案                                                                | 状态       | 特点                                   |
| ------------------------------------------------------------------- | ---------- | -------------------------------------- |
| Spring Cloud Config | 活跃       | Spring 生态原生支持，基于 Git 存储     |
| Nacos                           | 活跃       | 阿里开源，配置中心 + 服务发现二合一    |
| Apollo                    | 活跃       | 携程开源，配置管理、权限和审计能力较强 |
| K8s ConfigMap                                                       | 活跃       | Kubernetes 原生方案                    |
| Disconf / Qconf                                                     | 长期不活跃 | 不建议新项目使用                       |

**选型建议**：

- 只需配置中心 → **Apollo**（管理能力更细）或 **Nacos**（单机启动更轻）
- 需要配置中心 + 服务发现 → **Nacos**
- Spring Cloud 体系且追求简单 → **Spring Cloud Config**
- Kubernetes 环境 → **K8s ConfigMap 挂载 + 应用层文件监听**。ConfigMap 以 Volume 挂载时会被 kubelet 周期同步，最终可见时间取决于 kubelet 同步周期和本地缓存传播方式；环境变量方式和 `subPath` 挂载不会自动更新。热重载可以用 inotify 监听挂载文件，也可以用 Spring Cloud Kubernetes 通过 K8s Watch API 监听 ConfigMap 变更并触发刷新。

**Apollo vs Nacos vs Spring Cloud Config**

> **版本说明**：以下对比基于 Apollo 2.x、Nacos 2.x、Spring Cloud Config 4.x/5.x。Spring Boot 3 体系通常对应 Spring Cloud Config 4.x，Spring Boot 4 体系对应更新的 Spring Cloud 2025.x 发行列车；如果仍在 Spring Boot 2 体系，对应的是 Spring Cloud Config 3.x。

| 功能点       | Apollo                                     | Nacos                                        | Spring Cloud Config                  |
| ------------ | ------------------------------------------ | -------------------------------------------- | ------------------------------------ |
| 配置界面     | 支持（权限、审计、发布流程较完整）         | 支持                                         | 无（通常通过 Git 平台操作）          |
| 配置实时生效 | 支持（HTTP 长轮询，通常秒级感知）          | 支持（gRPC 变更通知 + 客户端拉取）           | 半实时（需触发 refresh 或 Bus 广播） |
| 版本管理     | 原生支持                                   | 原生支持                                     | 依赖 Git                             |
| 权限管理     | 支持（应用/命名空间/环境等多层粒度）       | 支持                                         | 依赖 Git 平台                        |
| 灰度发布     | 支持（规则更细）                           | 支持（1.1.0+，能力相对基础）                 | 不支持                               |
| 配置回滚     | 支持                                       | 支持                                         | 依赖 Git                             |
| 告警通知     | 支持                                       | 支持                                         | 不支持                               |
| 多语言       | 支持（Open API / 多语言客户端）            | 支持（Open API / 多语言客户端）              | 更偏 Spring 应用                     |
| 多环境       | 支持（通常物理隔离）                       | 支持（多用 Namespace 逻辑隔离）              | 需配合多 Git 仓库                    |
| 依赖组件     | MySQL（注册中心默认内嵌在 Config Service） | 外部 MySQL（生产推荐）/ 嵌入式 Derby + JRaft | Git + 可选消息队列                   |

**深度对比**：

1. **Apollo**：在权限模型、发布审计、发布前 diff、灰度规则等管理特性上更细，适合对配置治理要求较高的团队。多环境（FAT/UAT/PROD）物理隔离场景下，需为每个环境部署 Config Service、Admin Service 和独立数据库，运维门槛中等偏高
2. **Nacos**：配置 + 注册中心二合一，部署简单（单机模式仅一个 Jar 包）。生产集群推荐使用外部 MySQL；嵌入式 Derby + JRaft 更适合测试或小规模场景。Nacos 的 Namespace/Group/DataId 模型上手快，但环境隔离通常偏逻辑隔离
3. **Spring Cloud Config**：架构最简单（基于 Git），但实时性差，需要额外组件实现自动刷新
