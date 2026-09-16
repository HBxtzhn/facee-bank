光说不练假把式，现在就来撸一个 starter，实现自定义线程池

第一步，创建`threadpool-spring-boot-starter`工程

第二步，引入 Spring Boot 相关依赖

第三步，创建`ThreadPoolAutoConfiguration`

第四步，注册自动配置类。对于 Spring Boot 2.6 及更早版本，在`threadpool-spring-boot-starter`工程的 resources 包下创建`META-INF/spring.factories`文件；Spring Boot 2.7 及以上版本应使用 `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`，面向 Spring Boot 3.x 时自动配置类通常使用 `@AutoConfiguration` 标注。

最后新建工程引入`threadpool-spring-boot-starter`

测试通过！！！
