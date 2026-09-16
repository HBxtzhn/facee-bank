Spring 定义了 7 种传播行为（`Propagation`）：

| 传播行为 | 当前有事务 | 当前无事务 |
|---|---|---|
| REQUIRED（默认） | 加入 | 新建 |
| SUPPORTS | 加入 | 以非事务方式执行 |
| MANDATORY | 加入 | **抛异常** |
| REQUIRES_NEW | **挂起当前**，新建 | 新建 |
| NOT_SUPPORTED | **挂起当前**，非事务执行 | 非事务执行 |
| NEVER | **抛异常** | 非事务执行 |
| NESTED | 嵌套（savepoint） | 新建 |

## 高频考点：REQUIRES_NEW vs NESTED

- `REQUIRES_NEW`：两条**独立**事务，外层回滚不影响内层（内层已提交就保留）；
- `NESTED`：**同一**物理事务 + savepoint，内层回滚只回到 savepoint，外层回滚会连带内层。

## 最经典的失效场景

同类内部方法调用（`this.b()`）不走代理 ⇒ 传播行为与 `@Transactional` 全部失效。修法：注入自身代理、拆到另一个 Bean、或用 `AopContext.currentProxy()`。
