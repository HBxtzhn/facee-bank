| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
|---------|------|-----------|------|
| READ UNCOMMITTED | 可能 | 可能 | 可能 |
| READ COMMITTED | 解决 | 可能 | 可能 |
| REPEATABLE READ(默认) | 解决 | 解决 | 部分解决 |
| SERIALIZABLE | 解决 | 解决 | 解决 |
