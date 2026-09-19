1. **空回滚**：在 TCC 模式下，Try 阶段还没执行或者执行失败了，但是 TC 已经触发了 Cancel 操作。这时候 Cancel 方法要能识别出来 Try 没执行过，直接返回成功，不做任何业务操作，这就是空回滚处理。

2. **防悬挂**：当 Cancel 比 Try 先到达时（网络延迟导致），Cancel 已经执行了，这时候 Try 请求才到。如果不做处理，Try 就会执行成功并预留资源，但是这个资源永远都不会被 Confirm 或 Cancel 了，形成资源悬挂。防悬挂就是拒绝这种迟到的 Try 请求。

3. **实现方式**：一般通过一张事务控制表（branch_transaction_log），记录每个分支事务的状态。Cancel 执行时写入一条记录标记状态；Try 执行前检查这张表，如果已经有 Cancel 记录就拒绝执行，实现防悬挂；Cancel 执行前检查 Try 是否执行过，没有就跳过，实现空回滚。

4. **Seata 已经内置处理**：Seata TCC 模式框架层面已经处理了空回滚和防悬挂，开发者只需要在业务代码里做好幂等控制就行。

## 扩展知识

- **为什么会出现这种情况**：分布式环境下网络是不可靠的，Try 请求可能因为网络超时被 TC 认为失败，触发 Cancel。但实际上 Try 请求还在路上，或者已经到达参与者但还没来得及处理。这就是经典的分布式系统网络分区问题。
- **生产踩坑**：如果 TCC 的 Cancel 方法不做空回滚处理，可能会出现 Try 没执行但 Cancel 执行了，导致业务数据异常。比如 Try 阶段冻结了100块钱，Cancel 阶段要解冻，但 Try 没执行过，解冻就会出错。
- **代码示例**：

``java
// 防悬挂 + 空回滚 示例
@TwoPhaseBusinessAction(name = "deduct", commitMethod = "confirm", rollbackMethod = "cancel")
public boolean try_deduct(BusinessActionContext context, @BusinessActionContextParameter(paramName = "amount") double amount) {
    // 防悬挂：检查是否已经回滚过
    if (alreadyCancelled(context)) {
        return false; // 拒绝执行Try
    }
    // 正常Try逻辑：冻结金额
    freezeAmount(amount);
    return true;
}

public boolean cancel(BusinessActionContext context) {
    // 空回滚：如果Try没执行过，直接返回成功
    if (!tryExecuted(context)) {
        return true; // 空回滚，直接成功
    }
    // 正常Cancel逻辑：解冻金额
    unfreezeAmount(context);
    return true;
}
``
