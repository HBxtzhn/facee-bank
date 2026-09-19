## 追问 1：Seata 的 AT 模式和 TCC 模式怎么选？

AT 模式适合大部分 CRUD 业务，零侵入，开发效率高，但性能受限于全局锁。TCC 模式适合高并发场景（比如秒杀扣库存），性能更好，但开发成本高，每个接口要写 Try/Confirm/Cancel 三个方法。实际项目里普通业务用 AT，高并发场景用 TCC。

## 追问 2：Seata TC 挂了怎么办？

TC 是 Seata 的核心，必须做高可用部署。TC 集群模式下，事务数据存储在数据库（MySQL）中，多个 TC 节点共享同一份数据。一个 TC 挂了，其他 TC 节点可以继续工作。同时 TM 和 RM 有重试机制，短暂不可用不影响正在执行的事务。

## 追问 3：Seata 的 XID 是怎么在微服务之间传递的？

TM 开启全局事务时，TC 生成 XID 返回给 TM。TM 通过 Feign 的拦截器（SeataFeignObjectWrapper）把 XID 注入到 HTTP Header 里。下游服务通过 Filter 从 Header 里提取 XID，绑定到当前线程的上下文中。这样整条调用链的所有服务都关联到同一个全局事务。
