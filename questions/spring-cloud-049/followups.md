## 追问 1：Gateway 的 Predicate 有哪些？

常用的：Path（路径匹配）、Method（HTTP 方法匹配）、Header（请求头匹配）、Query（查询参数匹配）、After/Before/Between（时间匹配）、Weight（权重路由）、RemoteAddr（IP 匹配）。可以组合多个 Predicate，全部匹配才路由。

## 追问 2：Gateway 怎么做限流？

两种方案：第一，集成 Redis + Lua 脚本，用 RequestRateLimiter 过滤器实现令牌桶限流（基于 Redis 的 INCR 原子操作）。第二，集成 Sentinel，用 SentinelGatewayFilter 实现限流，支持 QPS 限流、热点参数限流。项目里用的 Sentinel，因为功能更丰富，而且支持动态配置。

## 追问 3：Gateway 的 GlobalFilter 和 GatewayFilter 有什么区别？

GatewayFilter 是针对单条路由的过滤器，只在这条路由上生效。GlobalFilter 是全局过滤器，对所有路由生效。GlobalFilter 常用于统一的鉴权、日志、跨域处理。GatewayFilter 用于特定路由的定制化逻辑（比如某个路由需要特殊的请求改写）。
