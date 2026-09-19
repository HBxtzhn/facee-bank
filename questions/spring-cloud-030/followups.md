## 追问 1：Feign 的底层原理是什么？

Feign 的核心是 JDK 动态代理。@FeignClient 注解的接口在 Spring 容器启动时，FeignClientFactoryBean 会为这个接口创建一个代理对象。代理对象内部用 Feign.Builder 构建 Feign 实例，包含 Contract（解析 Spring MVC 注解）、Encoder（请求体序列化）、Decoder（响应体反序列化）、Client（HTTP 发送）。调用方法时，代理的 invoke() 方法根据注解构建 HTTP 请求并发送。

## 追问 2：Feign 的超时怎么配置？

通过 
eign.client.config.default.connectTimeout（连接超时）和 
eign.client.config.default.readTimeout（读取超时）配置。也可以按服务名配置：
eign.client.config.order-service.connectTimeout=5000。如果用 OkHttp/Apache HttpClient，还需要配置连接池参数。

## 追问 3：Feign 怎么传递请求头？

三种方式：第一，用 @RequestHeader 注解在方法参数上声明；第二，用 @FeignClient(configuration = FeignConfig.class) 配置自定义的 RequestInterceptor，在拦截器里统一添加请求头（比如 Token）；第三，用 @Headers 注解在接口或方法上声明静态的请求头。
