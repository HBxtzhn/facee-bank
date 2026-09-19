## 追问 1：REQUIRED和REQUIRES_NEW在回滚表现上有什么区别？

REQUIRED内外共用同一事务，任何回滚全回滚。REQUIRES_NEW内层独立事务，内层回滚不影响外层，外层回滚不影响已提交内层。订单系统例子: 下单(REQUIRED)+记录日志(REQUIRES_NEW)，日志失败不影响下单，下单失败日志已提交也符合预期。

## 追问 2：NESTED和REQUIRES_NEW有什么区别？

NESTED内层是外层事务的savepoint，内层回滚只到savepoint不影响外层，但外层回滚时内层也回滚。REQUIRES_NEW完全独立事务外层回滚不影响内层。NESTED依赖JDBC savepoint只在JDBC环境生效。
