## 追问 1：MSET 和 Pipeline 有什么区别？

MSET 是服务端原生命令，保证原子性（一次性执行），只能设置 String 类型。Pipeline 是客户端技术，将多条命令打包发送减少网络往返，支持任意命令组合，但不保证原子性（逐条执行）。性能上 Pipeline 更灵活，MSET 更简洁。

## 追问 2：Cluster 模式下 MGET 会有什么问题？

Cluster 有 16384 个 slot，MGET 的多个 key 可能分布在不同 slot 的不同节点上。Redis Cluster 不支持跨 slot 的多 key 操作（会返回 `CROSSSLOT` 错误）。解决方案：① 使用 `{hashtag}` 让相关 key 落入同一 slot；② 客户端拆分为多个单 key 命令通过 Pipeline 发送。

## 追问 3：批量操作一次最多发多少条合适？

建议 500~1000 条/批。过多会导致：① 单次命令执行时间过长，阻塞其他请求；② 网络包过大，增加传输延迟；③ 内存占用增加。具体数值需根据 value 大小和网络状况压测确定。
