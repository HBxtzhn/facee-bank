1. **定义**：Spring Cloud Config 是 Spring Cloud 官方提供的分布式配置中心，把配置文件从应用中抽出来集中管理，支持多环境、动态刷新。

2. **架构**：分为 Config Server（服务端）和 Config Client（客户端）。Config Server 从 Git/SVN 仓库拉取配置文件，对外暴露 REST API；Config Client 启动时从 Config Server 拉取配置。

3. **配置存储**：默认用 Git 仓库存储配置文件，也支持 SVN 和本地文件系统。Git 的好处是天然支持版本管理和回滚。

4. **动态刷新**：配合 Spring Cloud Bus（消息总线）实现配置动态刷新。配置变更后，调用 /actuator/busrefresh 接口，通过 MQ 广播刷新事件给所有客户端。

5. **现状**：现在用的越来越少了，被 Nacos Config 和 Apollo 替代。因为它的动态刷新依赖 MQ，有延迟；没有管理界面；不支持灰度发布。

## 扩展知识

- **配置定位**：通过 pplication（应用名）+ profile（环境）+ label（Git 分支）定位配置文件。比如 order-service-dev.yml 在 master 分支。
- **加密配置**：Config Server 集成了 spring-cloud-starter-config-server 的加密能力，支持对称加密（AES）和非对称加密（RSA），配置文件里的敏感信息可以加密存储。
- **Failover**：Config Client 有 failover 机制，如果 Config Server 不可用，Client 会使用本地缓存的配置。
