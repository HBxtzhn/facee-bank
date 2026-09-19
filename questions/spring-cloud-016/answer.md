1. **AT 模式实现原理**：核心是数据源代理（DataSourceProxy）。Seata 代理了 JDBC 数据源，拦截 SQL 执行。一阶段执行时，先解析 SQL 拿到要操作的数据，查询 before image（执行前的数据快照），执行 SQL 后查询 after image（执行后的数据快照），把两个快照写入 undo_log 表，然后跟业务数据在同一个本地事务里提交。同时向 TC 注册分支事务，申请全局锁。

2. **二阶段提交**：TC 通知 RM 提交，RM 异步清理 undo_log（删除对应记录），非常快。

3. **二阶段回滚**：TC 通知 RM 回滚，RM 根据 undo_log 里的 before image 生成反向 SQL（比如 INSERT 变 DELETE，UPDATE 用 before image 的值还原），执行补偿。执行前要校验 after image 和当前数据是否一致（数据校验），如果不一致说明数据被其他事务修改了，需要人工介入。

4. **全局锁机制**：AT 模式的一阶段提交前，必须向 TC 申请全局锁。如果全局锁被其他事务持有，会重试；重试超时则回滚本地事务。全局锁保证了写隔离。

5. **SQL 解析**：Seata 内置了 SQL 解析引擎，支持 MySQL、Oracle、PostgreSQL 等主流数据库的 SQL 解析，从 SQL 中提取表名、条件、字段等信息来生成 image 查询。

## 扩展知识

- **源码关键类**：DataSourceProxy（数据源代理）→ ConnectionProxy（连接代理）→ StatementProxy（语句代理）→ ExecuteTemplate（执行模板，核心逻辑在这里）。ExecuteTemplate.execute() 里调用 BaseTransactionalExecutor，负责 before/after image 的查询和 undo_log 的写入。
- **全局锁的获取**：RM 在执行一阶段本地事务时，通过 ConnectionProxy 向 TC 发送 ranchRegisterRequest，TC 在数据库里记录全局锁。如果锁冲突，返回 LockKeyConflictException，ConnectionProxy 会重试。
- **undo_log 表结构**：包含 ranch_id（分支事务ID）、xid（全局事务ID）、efore_image（执行前数据快照，JSON格式）、fter_image（执行后数据快照，JSON格式）、context（上下文信息，包含序列化方式）。
