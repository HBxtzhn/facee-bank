1. **来源不同**：Feign 最初是 Netflix 开发的开源项目（com.netflix.feign），后来贡献给了开源社区。OpenFeign 是社区接手续维护的版本（io.github.openfeign），是 Feign 的增强版。

2. **Spring MVC 注解支持**：原版 Feign 使用自己的注解（@RequestLine、@Headers）。OpenFeign 增加了对 Spring MVC 注解的支持（@GetMapping、@PostMapping、@PathVariable、@RequestBody 等），跟 Spring Boot 无缝集成。

3. **Spring Cloud 集成**：Spring Cloud 使用的是 OpenFeign（spring-cloud-starter-openfeign），不是原版 Feign。OpenFeign 跟 Spring Cloud 的注册发现、负载均衡、配置中心等组件深度集成。

4. **功能增强**：OpenFeign 增加了一些功能——更好的 Spring Boot 自动配置、Contract 扩展（支持 Spring MVC 注解）、与 Spring Cloud LoadBalancer 的集成等。

5. **现状**：原版 Feign 已经停更（最后版本 8.x），OpenFeign 持续更新（当前 12.x+）。Spring Cloud 项目里用的都是 OpenFeign。

## 概念对比

| 特性 | Feign（Netflix） | OpenFeign（社区） |
|------|-----------------|------------------|
| 维护方 | Netflix（已停更） | 开源社区（活跃） |
| 注解 | @RequestLine | 支持 Spring MVC 注解 |
| Spring Cloud | 早期集成 | 深度集成 |
| 版本 | 8.x（停止） | 12.x+（持续更新） |
| Maven坐标 | com.netflix.feign | io.github.openfeign |

## 扩展知识

- **Contract 机制**：Feign 通过 Contract 接口定义如何解析接口上的注解。原版 Feign 的 Contract.Default 解析 @RequestLine；OpenFeign 的 SpringMvcContract 解析 Spring MVC 注解。
- **迁移**：如果从老版本的 Spring Cloud（使用 Netflix Feign）迁移到新版本，需要把 
eign-core 替换为 spring-cloud-starter-openfeign，注解不需要改（Spring Cloud Netflix 版本也支持了部分 Spring MVC 注解）。
