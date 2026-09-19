## 追问 1：没有 Spring Boot 能用 Spring Cloud 吗？

理论上不行，Spring Cloud 的设计就是基于 Spring Boot 的自动配置机制。每个 Spring Cloud 组件都提供了 Spring Boot Starter，通过自动配置简化集成。没有 Spring Boot，你需要手动配置大量东西，失去了 Spring Cloud 的便利性。

## 追问 2：Spring Boot 的自动配置原理是什么？

核心是 @EnableAutoConfiguration 注解，它会导入 AutoConfigurationImportSelector，读取 META-INF/spring.factories（Spring Boot 2.x）或 META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports（Spring Boot 3.x）文件中注册的自动配置类。这些配置类通过 @Conditional 注解判断条件（比如 classpath 下有没有某个类），满足条件就自动创建 Bean。

## 追问 3：你的项目里 Spring Boot 用的什么版本？

项目里用的 Spring Boot 2.7.x，搭配 Spring Cloud 2021.0.x。Spring Boot 3.x 要求 Java 17+，实际项目还是 Java 8/11，所以没升。升级 Spring Boot 大版本要注意很多依赖的兼容性，比如 javax 包变成了 jakarta，一些 API 也废弃了。
