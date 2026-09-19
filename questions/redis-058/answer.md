- **Java 常用客户端**：
  1. **Jedis**：早期最流行的客户端，API 简单直接，同步阻塞模型，线程不安全（需使用连接池）。
  2. **Lettuce**：Spring Boot 2.x+ 默认客户端，基于 Netty，支持异步/同步、线程安全、连接复用、支持集群/哨兵模式。
  3. **Redisson**：功能最丰富的客户端，提供分布式锁、集合容器、远程服务等高级功能，API 友好。
- **推荐选择**：
  - 简单 CRUD → Lettuce（Spring Boot 默认）
  - 需要分布式锁/高级功能 → Redisson
  - 老项目维护 → Jedis

## 扩展知识

**Jedis vs Lettuce 架构对比**：
- Jedis：每个连接是独立的 TCP 连接（阻塞 I/O），多线程需使用 `JedisPool` 连接池
- Lettuce：基于 Netty 的 NIO 连接，一个连接可被多个线程共享（线程安全），支持命令管道和异步调用

**Redisson 的核心特性**：
- 分布式锁（可重入锁、公平锁、联锁、红锁）
- 分布式集合（ConcurrentMap、Set、List 等 Java 集合接口实现）
- 分布式原子变量、信号量、CountDownLatch
- 远程服务调用（RPC）
- 发布/订阅、Bloom Filter

**连接池配置要点**：
- 最大连接数：根据 Redis 服务端 `maxclients` 和业务并发量设置
- 空闲超时：及时回收空闲连接
- 超时设置：连接超时、读取超时、重试策略

## 对比表格

| 特性 | Jedis | Lettuce | Redisson |
|------|-------|---------|----------|
| I/O 模型 | 阻塞 I/O | Netty NIO | Netty NIO |
| 线程安全 | 否（需连接池） | 是 | 是 |
| 异步支持 | 有限 | 完整 | 完整 |
| 集群支持 | 支持 | 支持 | 支持 |
| 分布式锁 | 需自行实现 | 需自行实现 | 内置丰富实现 |
| Spring Boot 默认 | 否（1.x） | 是（2.x+） | 否 |
| 包大小 | 小 | 中（依赖 Netty） | 大 |
