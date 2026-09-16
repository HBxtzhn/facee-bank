Redis 从 4.0 版本开始，支持通过 Module 来扩展其功能以满足特殊的需求。这些 Module 以动态链接库（so 文件）的形式被加载到 Redis 中，这是一种非常灵活的动态扩展功能的实现方式，值得借鉴学习！

我们每个人都可以基于 Redis 去定制化开发自己的 Module，比如实现搜索引擎功能、自定义分布式锁和分布式限流。

目前，被 Redis 官方推荐的 Module 有：

- RediSearch：用于实现搜索引擎的模块。
- RedisJSON：用于处理 JSON 数据的模块。
- RedisGraph：用于实现图形数据库的模块。
- RedisTimeSeries：用于处理时间序列数据的模块。
- RedisBloom：用于实现布隆过滤器的模块。
- RedisAI：用于执行深度学习/机器学习模型并管理其数据的模块。
- RedisCell：用于实现分布式限流的模块。
- ……

关于 Redis 模块的详细介绍，可以查看官方文档：。
