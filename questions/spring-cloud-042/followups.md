## 追问 1：为什么从 Hystrix 迁移到 Sentinel？

三个原因：第一，Hystrix 停更了，没有新功能和 Bug 修复；第二，Hystrix 不支持限流，而限流是微服务必需的能力；第三，Hystrix 的规则只能通过代码配置，修改要重新部署，Sentinel 支持 Nacos 动态配置，规则实时生效。

## 追问 2：Sentinel 的信号量隔离够用吗？

大部分场景够用。信号量隔离的开销很小（只是一个计数器），性能比线程池隔离好很多。线程池隔离的好处是当一个服务超时导致线程阻塞时，不影响其他服务。但 Sentinel 的信号量隔离配合超时控制，也能达到类似效果。如果确实需要强隔离，Sentinel 也支持线程池隔离（@SentinelResource 的 	hreadPool 参数）。

## 追问 3：Sentinel 的热点参数限流是什么？

热点参数限流是针对某个请求参数的值做限流。比如商品详情接口，对热门商品（ID=1001）的请求做单独限流，防止大量请求集中在同一个商品上。通过 @SentinelResource 的 lockHandler 配合 ParamFlowRule 实现。可以设置不同参数值的阈值，比如热门商品阈值100 QPS，普通商品阈值1000 QPS。
