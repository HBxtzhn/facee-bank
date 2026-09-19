1. **定位不同**：Spring Boot 是快速开发框架，简化 Spring 应用的创建和配置，用于开发单个应用；Spring Cloud 是微服务治理框架，解决分布式环境下的服务治理问题，依赖多个 Spring Boot 应用协同工作。

2. **关系**：Spring Cloud 基于 Spring Boot，Spring Cloud 的每个组件都是一个 Spring Boot 应用。Spring Boot 解决的是"快速创建"，Spring Cloud 解决的是"协同治理"。

3. **功能范围**：Spring Boot 关注单个应用内的自动配置、内嵌服务器、Starter 依赖；Spring Cloud 关注应用间的注册发现、配置管理、负载均衡、熔断降级、网关路由。

4. **独立使用**：Spring Boot 可以独立使用，不需要 Spring Cloud；Spring Cloud 必须依赖 Spring Boot（或者说 Spring Cloud 的每个微服务都是一个 Spring Boot 应用）。

5. **类比**：Spring Boot 像是造一辆车，Spring Cloud 像是管理一个车队（调度、导航、通信、故障处理）。

## 扩展知识

- **开发流程**：先用 Spring Boot 开发各个微服务，然后用 Spring Cloud 把这些服务组织起来，实现服务注册发现、配置管理、网关路由等功能。
- **依赖管理**：Spring Cloud 有自己的 BOM（Bill of Materials）管理版本，引入 Spring Cloud 的依赖管理后，各个组件的版本会自动对齐，不需要手动指定每个组件的版本号。

## 概念对比

| 维度 | Spring Boot | Spring Cloud |
|------|------------|-------------|
| 定位 | 快速开发单个应用 | 微服务整体解决方案 |
| 使用场景 | 单体应用、微服务中的单个服务 | 微服务架构的整体治理 |
| 核心能力 | 自动配置、内嵌服务器、Starter | 注册发现、配置中心、网关、熔断 |
| 能否独立使用 | 可以 | 不能，依赖 Spring Boot |
| 关注点 | 应用内的简化 | 应用间的协调 |
