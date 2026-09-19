## 追问 1：Hystrix 为什么被淘汰了？

三个原因：第一，Netflix 停止维护了，不再有新功能和 Bug 修复；第二，线程池隔离模型太重，服务多了线程数爆炸；第三，功能不够全，比如没有系统级别的保护（Load Shedding）、没有动态规则配置。Sentinel 和 Resilience4j 在这些方面做得更好。

## 追问 2：Hystrix 的线程池隔离是怎么工作的？

Hystrix 为每个服务调用（Command Group）分配一个独立的线程池。比如调用订单服务用 order-pool，调用库存服务用 stock-pool。当订单服务挂了，order-pool 的线程被占满，但 stock-pool 不受影响，其他服务还能正常调用。这就是舱壁模式（Bulkhead），用资源隔离防止故障扩散。

## 追问 3：Hystrix 和 Sentinel 的区别？

隔离方式：Hystrix 用线程池隔离（重），Sentinel 用信号量隔离（轻）。限流：Hystrix 不支持限流，Sentinel 支持 QPS 限流、线程数限流、热点参数限流等。熔断：两者都支持，但 Sentinel 支持更多策略（慢调用比例、异常比例、异常数）。规则配置：Hystrix 通过代码配置，Sentinel 支持动态配置（Nacos/Apollo）。
