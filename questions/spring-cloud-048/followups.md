## 追问 1：Zuul 和 Gateway 的核心区别是什么？

线程模型不同。Zuul 基于 Servlet，同步阻塞，每个请求一个线程。Gateway 基于 WebFlux + Netty，异步非阻塞，少量线程处理大量并发。性能上 Gateway 远高于 Zuul。功能上两者类似（路由 + 过滤器），但 Gateway 的过滤器链更灵活（支持 Predicate + Filter 组合）。

## 追问 2：Zuul 的过滤器有哪些类型？

四种：pre（请求路由前执行，做鉴权、日志）、route（请求路由时执行，实际转发请求）、post（请求路由后执行，处理响应）、error（发生错误时执行）。过滤器的执行顺序通过 
ilterOrder() 方法指定。

## 追问 3：你的项目从 Zuul 迁移到 Gateway 了吗？

是的，去年从 Zuul 迁移到了 Gateway。主要原因是 Zuul 停更了，而且高并发场景下 Zuul 的性能瓶颈明显（线程数经常打满）。迁移过程主要是改过滤器和路由配置，业务逻辑基本不用改。迁移后网关的吞吐量提升了 3 倍以上。
