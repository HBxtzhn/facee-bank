## 隔离级别与现象

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
|---|---|---|---|
| READ UNCOMMITTED | ✓ | ✓ | ✓ |
| READ COMMITTED | ✗ | ✓ | ✓ |
| REPEATABLE READ（MySQL 默认） | ✗ | ✗ | ✗（InnoDB 用间隙锁基本避免） |
| SERIALIZABLE | ✗ | ✗ | ✗ |

## MVCC 三要素

1. **隐藏列**：每行有 `DB_TRX_ID`（最后修改事务）与 `DB_ROLL_PTR`（指向 undo log 版本链）；
2. **undo log**：串成版本链，可回溯历史版本；
3. **ReadView**：快照，包含当前活跃事务 ID 集合 `m_ids`、`min_trx_id`、`max_trx_id`、创建者 ID。

可见性判断：沿版本链找第一个 `trx_id < min_trx_id`（已提交）或"是自己的修改"的版本。

## RC 与 RR 的本质差异

- **READ COMMITTED**：**每次 SELECT 都新建 ReadView** ⇒ 能看到别人新提交的数据；
- **REPEATABLE READ**：**第一次 SELECT 建 ReadView 并复用** ⇒ 整个事务看到同一快照。

## 快照读 vs 当前读

- 快照读（普通 `SELECT`）走 MVCC，不加锁；
- 当前读（`SELECT ... FOR UPDATE`、`UPDATE`、`DELETE`）读最新版本并加锁，幻读由**间隙锁（Gap Lock）** 阻止。
