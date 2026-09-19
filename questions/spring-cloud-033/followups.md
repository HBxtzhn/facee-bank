## 追问 1：为什么 Spring Cloud 要用 OpenFeign 替代 Feign？

Netflix Feign 停更了，Spring Cloud 需要持续维护的版本。OpenFeign 是社区接手的增强版，功能更全，跟 Spring 生态集成更好。而且 OpenFeign 支持 Spring MVC 注解，开发者不需要学新的注解体系。

## 追问 2：OpenFeign 支持哪些 HTTP 客户端？

默认用 JDK 的 URLConnection（不支持连接池），推荐替换为 OkHttp（
eign.okhttp.enabled=true）或 Apache HttpClient（
eign.httpclient.enabled=true），支持连接池、超时配置、连接复用。Spring Cloud 2022+ 还支持 Apache HttpClient 5。

## 追问 3：OpenFeign 的 Contract 是什么？

Contract 是 Feign 的注解解析器，定义如何把接口上的注解转换成 HTTP 请求。OpenFeign 的 SpringMvcContract 能解析 @RequestMapping、@GetMapping、@PostMapping、@PathVariable、@RequestParam、@RequestBody 等 Spring MVC 注解，让 Feign 的使用体验跟写 Controller 一样。
