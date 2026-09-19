## 追问 1：undo_log 什么时候清理？

二阶段提交成功后，RM 异步删除对应的 undo_log 记录。如果二阶段回滚了，执行完反向 SQL 后也会删除 undo_log。undo_log 只是临时数据，不需要长期保存。

## 追问 2：AT 模式回滚时数据校验失败怎么处理？

数据校验失败说明有脏写，Seata 不会自动回滚，会抛出 BranchRollbackFailed_Retriable 或 BranchRollbackFailed_Unretriable 异常。可重试的异常 TC 会定时重试，不可重试的需要人工介入。生产环境要避免这种情况，关键是保证全局锁机制正常工作。

## 追问 3：Seata 回滚跟 Spring 本地事务回滚有什么区别？

Spring 本地事务回滚是数据库层面的，通过 JDBC 的 connection.rollback() 实现，回滚的是当前事务里的所有 SQL。Seata 分布式事务回滚是应用层面的补偿，通过执行反向 SQL 来"撤销"已经提交的本地事务。本质区别是 Seata 的一阶段已经提交了，不能用数据库的 rollback，只能用补偿的方式。
