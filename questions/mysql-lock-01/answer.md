## 锁类型

- **按粒度**：表锁、行锁、间隙锁、临键锁（Next-Key = 记录锁 + 间隙锁）；
- **按模式**：共享锁 S、排他锁 X、意向锁 IS/IX（表级，用于快速判断能否加表锁）；
- **其他**：插入意向锁、自增锁（`innodb_autoinc_lock_mode`）。

## 排查死锁

```sql
SHOW ENGINE INNODB STATUS\G          -- 看 LATEST DETECTED DEADLOCK 段
SELECT * FROM performance_schema.data_locks;
SELECT * FROM performance_schema.data_lock_waits;
```

## 常见死锁形态与修法

| 形态 | 原因 | 修法 |
|---|---|---|
| 两个事务反向更新两行 | 加锁顺序不一致 | **统一加锁顺序**（如按主键升序） |
| 无索引更新 | 退化为锁全表/大量行 | 补索引，缩小锁范围 |
| 间隙锁冲突 | RR 级别下范围条件加 Next-Key | 缩小范围条件，或评估降为 RC |

核心原则：**让所有事务以相同顺序、尽可能小的范围加锁**。
