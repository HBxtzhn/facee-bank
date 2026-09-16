备份脚本跑成功，只说明生成了文件。文件能不能用，要靠恢复演练回答。

一个最小演练可以按这个流程走：

1. 准备一台隔离机器，安装相同大版本的 MySQL。
2. 拉取最近一次全量备份和对应 binlog，确认解密密钥、账号、证书和对象存储访问方式都可用。
3. 恢复全量备份，记录耗时。
4. 回放 binlog 到指定时间点，记录耗时。
5. 校验关键库表数量、关键业务 SQL、存储过程、事件、触发器、账号、角色和权限。
6. 检查 `charset`、`collation`、`time_zone`、`sql_mode`、只读开关和网络隔离，确保恢复实例不会被真实业务流量误连。
7. 用应用连接恢复实例，跑一组只读冒烟接口。
8. 记录这次演练的实际 RTO、可恢复到的时间点、失败步骤和人工操作。

校验不要只看 MySQL 能不能启动。至少要查几类数据：

```sql
-- 关键表行数
SELECT COUNT(*) FROM order_db.orders;

-- 最近写入时间
SELECT MAX(created_at) FROM order_db.orders;

-- 存储过程和函数
SHOW PROCEDURE STATUS WHERE Db = 'order_db';
SHOW FUNCTION STATUS WHERE Db = 'order_db';

-- 事件
SHOW EVENTS FROM order_db;

-- 触发器
SHOW TRIGGERS FROM order_db;

-- 账号、角色和权限
SELECT user, host FROM mysql.user;
SHOW GRANTS FOR 'app_user'@'%';

-- 关键环境参数
SELECT
  @@character_set_server,
  @@collation_server,
  @@time_zone,
  @@sql_mode,
  @@read_only,
  @@super_read_only;
```

如果业务有对账表、流水表、库存表，要优先校验这些表。恢复演练的目标很具体：尽早发现“备份少对象、binlog 缺文件、权限恢复不了、导入耗时远超预期”这类会在事故里放大的问题。
