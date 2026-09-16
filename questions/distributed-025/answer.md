分布式限流针对的是分布式/微服务应用架构。在这种架构下，一种服务可能会被部署多份，如果需要控制全局配额，就要让多个实例共享限流状态。不过，并非所有微服务场景都必须使用分布式限流：如果配额可以按实例数均分（例如总配额 1000 QPS，10 个实例各限制 100 QPS），单机限流在性能和实现复杂度上反而更有优势。

分布式限流常见的方案：

- **借助中间件限流**：可以借助 Sentinel 或者使用 Redis 来自己实现对应的限流逻辑。
- **网关层限流**：比较常用的一种方案，直接在网关层把限流给安排上了。不过，网关层限流通常也需要借助中间件/框架。就比如 Spring Cloud Gateway 的分布式限流实现 `RedisRateLimiter` 就是基于 Redis+Lua 来实现的，再比如 Spring Cloud Gateway 还可以整合 Sentinel 来做限流。

如果你要基于 Redis 来手动实现限流逻辑的话，建议配合 Lua 脚本来做。

**为什么建议 Redis+Lua 的方式？** 主要有两点原因：

- **减少了网络开销**：我们可以利用 Lua 脚本来批量执行多条 Redis 命令，这些 Redis 命令会被提交到 Redis 服务器一次性执行完成，大幅减小了网络开销。
- **原子性**：一段 Lua 脚本可以视作一条命令执行，一段 Lua 脚本执行过程中不会有其他脚本或 Redis 命令同时执行，保证了操作不会被其他指令插入或打扰。

需要注意的是，Lua 脚本在 Redis 中会原子执行，也意味着脚本执行期间会阻塞 Redis 的其他操作。因此，限流脚本要尽量保持轻量，避免复杂计算或大范围遍历，并结合 Redis 的 `lua-time-limit` 配置防止脚本执行时间失控。

我这里就不放具体的限流脚本代码了，网上也有很多现成的优秀的限流脚本供你参考，就比如 Apache 网关项目 ShenYu 的 RateLimiter 限流插件就基于 Redis + Lua 实现了令牌桶算法/并发令牌桶算法、漏桶算法、滑动窗口算法。

> ShenYu 地址:

另外，如果不想自己写 Lua 脚本的话，也可以直接利用 Redisson 中的 `RRateLimiter` 来实现分布式限流，其底层实现就是基于 Lua 代码+令牌桶算法。

Redisson 是一个开源的 Java 语言 Redis 客户端，提供了很多开箱即用的功能，比如 Java 中常用的数据结构实现、分布式锁、延迟队列等等。并且，Redisson 还支持 Redis 单机、Redis Sentinel、Redis Cluster 等多种部署架构。

`RRateLimiter` 的使用方式非常简单。我们首先需要获取一个`RRateLimiter`对象，直接通过 Redisson 客户端获取即可。然后，设置限流规则就好。

```java
// 获取一个许可，如果超过限流器的速率则会等待
// acquire()是同步方法，对应的异步方法：acquireAsync()
rateLimiter.acquire(1);
// 尝试在 5 秒内获取一个许可，如果成功则返回 true，否则返回 false
// tryAcquire()是同步方法，对应的异步方法：tryAcquireAsync()
boolean res = rateLimiter.tryAcquire(1, 5, TimeUnit.SECONDS);
```

`RRateLimiter` 的限流精度会受到 Redis 性能和网络延迟影响，在超高并发场景下可能出现轻微超限。Redis 故障或主从切换时，`tryAcquire()` 可能抛出异常，业务层需要提前确定降级策略：回退到本地限流、直接拒绝，还是短时间放行，取决于业务对稳定性和可用性的取舍。
