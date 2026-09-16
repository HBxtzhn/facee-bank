单机限流针对的是单体架构应用。

单机限流可以直接使用 Google Guava 自带的限流工具类 `RateLimiter` 。 `RateLimiter` 基于令牌桶算法，可以应对突发流量。

> Guava 地址：

除了最基本的令牌桶算法(平滑突发限流)实现之外，Guava 的`RateLimiter`还提供了 **平滑预热限流** 的算法实现。

平滑突发限流就是按照指定的速率放令牌到桶里，而平滑预热限流会有一段预热时间，预热时间之内，速率会逐渐提升到配置的速率。

下面通过两个简单的示例来了解。

我们直接在项目中引入 Guava 相关依赖即可使用。

下面是一个简单的 Guava 平滑突发限流的 Demo。第一次输出等待时间为 `0.0s`，是因为 `RateLimiter.create(5)` 创建后桶里初始就有可用令牌。

下面是一个简单的 Guava 平滑预热限流的 Demo。

> Bucket4j 地址：

相对于 Guava 的限流工具类来说，Bucket4j 提供的限流功能更加全面。不仅支持单机限流和分布式限流，还可以集成监控，搭配 Prometheus 和 Grafana 使用。

不过，毕竟 Guava 也只是一个功能全面的工具类库，其提供的开箱即用的限流功能在很多单机场景下还是比较实用的。

Spring Cloud Gateway 的内置限流器 `RedisRateLimiter` 基于 Redis + Lua 实现。Bucket4j 和 Resilience4j 更多是通过独立扩展或 Spring 生态组件与网关集成，并不是 Spring Cloud Gateway 内置限流器的替代演进关系。

Resilience4j 是一个轻量级的容错组件，其灵感来自于 Hystrix。自Netflix 宣布不再积极开发 Hystrix 之后，Spring 官方和 Netflix 都更推荐使用 Resilience4j 来做限流熔断。

> Resilience4j 地址:

一般情况下，为了保证系统的高可用，项目的限流和熔断都是要一起做的。

Resilience4j 不仅提供限流，还提供了熔断、负载保护、自动重试等保障系统高可用开箱即用的功能。并且，Resilience4j 的生态也更好，很多网关都使用 Resilience4j 来做限流熔断的。

因此，在绝大部分场景下 Resilience4j 或许会是更好的选择。如果是一些比较简单的限流场景的话，Guava 或者 Bucket4j 也是不错的选择。
