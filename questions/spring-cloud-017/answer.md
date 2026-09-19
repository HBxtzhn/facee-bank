1. **AT 模式回滚**：TC 通知 RM 回滚 → RM 根据 XID 和 BranchId 查询 undo_log → 反序列化 before image → 生成反向 SQL → 校验 after image 与当前数据是否一致 → 执行反向 SQL → 删除 undo_log。

2. **反向 SQL 生成规则**：INSERT → DELETE（根据主键删除）；DELETE → INSERT（根据 before image 插入）；UPDATE → UPDATE（用 before image 的值还原字段）。

3. **数据校验**：回滚前必须校验 after image 和当前数据是否一致。如果不一致，说明数据被其他全局事务修改过（可能是脏写），这时候不能自动回滚，需要人工介入。

4. **TCC 模式回滚**：调用 Cancel 方法，Cancel 方法的逻辑由开发者自己实现。框架层面会检查 Try 是否执行过（空回滚处理），检查是否已经回滚过（幂等处理）。

5. **回滚超时**：如果回滚失败（比如数据库连接断了），TC 会定时重试回滚。可以通过 service.rollbackRetryCount 配置重试次数。

## 扩展知识

- **回滚失败的处理**：如果自动回滚失败（数据校验不通过），Seata 会把事务状态标记为 RollbackRetrying，TC 定时重试。如果一直重试失败，最终状态变为 RollbackFailed，需要人工介入。
- **死锁问题**：AT 模式下，如果两个全局事务互相持有对方需要的全局锁，会形成死锁。Seata 通过锁超时机制解决——全局锁有超时时间（默认60秒），超时后自动释放。
- **回滚的性能**：二阶段回滚比二阶段提交慢，因为要执行反向 SQL。所以生产环境要尽量减少回滚的发生，做好前置校验。
