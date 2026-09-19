## 追问 1：TraceId 是怎么跨服务传递的？

以 Feign 为例，Sleuth 会通过 Feign 的 RequestInterceptor 拦截器，在每次 Feign 调用时自动把当前上下文的 TraceId、SpanId 注入到 HTTP 请求头里。下游服务收到请求后，通过 Servlet Filter 从请求头里提取这些值，放入自己的上下文中。这样整条链路的 TraceId 就串起来了。

## 追问 2：实际项目的链路追踪是怎么做的？

项目里用的 SkyWalking，因为它是 Java Agent 方式接入，业务代码零侵入，只需要在启动命令里加 -javaagent:/path/to/skywalking-agent.jar 就行。它能自动追踪 Spring MVC、MyBatis、Redis、MQ 等组件的调用，还能看到 SQL 执行耗时、慢接口统计，功能很全。

## 追问 3：链路追踪的采样策略有哪些？

常见的有：固定比例采样（比如 10% 的请求都追踪）；固定数量采样（每分钟只追踪1000个）；自适应采样（正常情况下低采样率，出错时提高采样率）；全量采样（测试环境用，每个请求都追踪）。生产环境一般用固定比例或自适应采样，平衡性能和可观测性。
