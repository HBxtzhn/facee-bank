## 追问 1：线上遇到过这个问题吗？怎么解决的？

遇到过。解决方案是在每个服务启动后做预热——写一个 ApplicationRunner，启动后遍历所有 FeignClient，主动调用一次健康检查接口。这样第一次调用的初始化工作在启动阶段就完成了，不影响正式请求的响应时间。

## 追问 2：Feign 的连接池怎么配置？

引入 OkHttp 依赖，配置 
eign.okhttp.enabled=true，然后配置连接池参数：

``yaml
feign:
  okhttp:
    enabled: true
  client:
    config:
      default:
        connectTimeout: 5000
        readTimeout: 10000
``

OkHttp 默认有连接池（最大空闲连接数5，空闲超时5分钟），可以根据业务调整。

## 追问 3：除了预热，还有什么方案？

还有几种方案：第一，关闭懒加载（spring.cloud.loadbalancer.lazy-load=false），启动时初始化所有 LoadBalancer；第二，用 @LoadBalancerClient 配置自定义的 LoadBalancer，在初始化时做一些预热操作；第三，调整 Sentinel/Hystrix 的超时时间，让第一次调用不被误判为超时。最推荐的还是预热方案，简单有效。
