## 追问 1：RR默认级别能防超卖吗？

不能。隔离级别只解决读的问题不解决写并发问题。两个事务同时读到库存=1，各自判断>0都执行扣减最终库存=-1。防超卖需用行锁SELECT...FOR UPDATE或乐观锁version字段或Redis DECR原子操作。

## 追问 2：InnoDB在RR下怎么解决幻读的？真完全解决了？

通过Next-Key Lock(间隙锁+行锁)。SELECT...FOR UPDATE时不仅锁存在行还锁行间间隙阻止insert。但不是完全解决: 普通SELECT(快照读)不产生间隙锁; 间隙锁只在范围加锁时生效。InnoDB RR解决了写操作的幻读，但读操作是快照读和SQL标准RR有差异。
