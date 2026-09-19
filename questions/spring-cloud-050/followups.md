## 追问 1：Gateway 的限流和 Sentinel 的限流有什么区别？

Gateway 的限流是在网关层做的，限制的是进入系统的总流量（粗粒度）。Sentinel 的限流可以在网关层做（SentinelGatewayFilter），也可以在应用层做（细粒度，按接口、按用户、按参数）。实践中两个都会用——Gateway 层用 Sentinel 的 SentinelGatewayFilter 做入口限流，应用层用 Sentinel 做细粒度限流。

## 追问 2：Gateway 怎么做统一鉴权？

通过 GlobalFilter 实现。写一个 AuthGlobalFilter，在 
ilter() 方法里从请求头提取 Token（JWT），校验 Token 的合法性（签名、过期时间）。校验通过把用户信息放入请求头传递给后端服务，校验失败直接返回 401。这样后端服务不需要重复实现鉴权逻辑。

## 追问 3：Gateway 怎么做灰度路由？

通过自定义 Predicate 和 Filter 实现。给灰度实例在 Nacos 注册时打上 
ersion=gray 元数据。自定义 GlobalFilter 根据请求头里的灰度标识（X-Gray-Version）或用户属性，选择路由到灰度实例还是正式实例。也可以结合 Nacos 的元数据路由，动态调整灰度比例。
