## 失效清单

| 场景 | 原因 |
|---|---|
| 同类内部调用 `this.b()` | 不经过代理 |
| 方法非 `public` | 代理只拦截 public 方法（CGLIB 亦如此约定） |
| 异常被 catch 未抛出 | 代理感知不到失败 |
| 抛出**受检异常** | 默认只对 `RuntimeException`/`Error` 回滚 |
| 传播行为为 `NOT_SUPPORTED`/`NEVER` | 本身就不在事务里 |
| 多数据源未配事务管理器 | 用了默认的 DataSourceTransactionManager |
| 类未被 Spring 管理（`new` 出来的） | 没有代理 |
| 数据库引擎不支持事务（MyISAM） | 存储引擎层面 |

## 排查思路

1. 先确认**代理是否存在**：`AopUtils.isAopProxy(bean)`，或启动时打印 Bean 类型；
2. 打开事务日志：`logging.level.org.springframework.transaction=DEBUG`，观察是否打出 `Creating new transaction`；
3. 确认异常类型与 `rollbackFor` 是否匹配；
4. 检查是否有 `try/catch` 吞掉了异常。

修法优先级：**先修调用方式**（拆 Bean）> 调整 `rollbackFor` > 显式编程式事务（`TransactionTemplate`）。
