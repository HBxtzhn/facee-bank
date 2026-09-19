1. **隔离策略**：Hystrix 用线程池隔离（每个服务一个线程池），隔离性强但线程开销大。Sentinel 用信号量隔离（计数器），开销小但隔离性弱一些。Sentinel 也支持线程池隔离（需要额外配置）。

2. **限流能力**：Hystrix 不支持限流（只有熔断）。Sentinel 支持丰富的限流——QPS 限流、线程数限流、热点参数限流、系统自适应保护、网关限流。

3. **熔断策略**：Hystrix 只支持异常比例熔断。Sentinel 支持三种——慢调用比例、异常比例、异常数。

4. **规则配置**：Hystrix 通过代码配置，修改需要重新部署。Sentinel 支持动态配置（Nacos、Apollo、Zookeeper），规则实时生效，不需要重启。

5. **实时监控**：Hystrix 有 Hystrix Dashboard（基于 Servlet，功能有限）。Sentinel 有 Sentinel Dashboard（功能更全，支持实时查看 QPS、RT、线程数，还能在线修改规则）。

6. **社区和维护**：Hystrix 已停更。Sentinel 是阿里开源的 Apache 项目，持续更新，社区活跃，国内用的多。

## 概念对比

| 特性 | Hystrix | Sentinel |
|------|---------|----------|
| 隔离策略 | 线程池隔离 | 信号量隔离（默认） |
| 限流 | 不支持 | QPS/线程数/热点参数 |
| 熔断策略 | 异常比例 | 慢调用/异常比例/异常数 |
| 规则配置 | 代码（静态） | 动态（Nacos/Apollo） |
| 实时监控 | Hystrix Dashboard | Sentinel Dashboard |
| 控制台 | 简单 | 功能丰富，支持在线修改 |
| 社区 | 停更 | 活跃 |

## 扩展知识

- **迁移建议**：从 Hystrix 迁移到 Sentinel，主要改动：注解从 @HystrixCommand 改为 @SentinelResource；配置从代码改为 Nacos 动态规则；线程池隔离改为信号量隔离（大部分场景够用）。Sentinel 提供了 sentinel-core 和 spring-cloud-starter-alibaba-sentinel 方便集成。
- **Sentinel 的自适应保护**：Sentinel 可以监控系统的 Load、CPU 使用率、平均 RT、总 QPS、线程数，当系统负载超过阈值时自动拒绝请求，保护系统不被压垮。这是 Hystrix 没有的功能。
