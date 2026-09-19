## 追问 1：模糊搜索接口用LIKE '%${keyword}%'有什么风险？

攻击者输入`' OR '1'='1`导致LIKE '%' OR '1'='1 %'匹配所有记录返回全量。正确: `LIKE CONCAT('%', #{keyword}, '%')`把%拼在Java参数里或SQL中用CONCAT。

## 追问 2：${}什么场景必须用？

动态表名(分表monthly_order_${month})、动态列名(ORDER BY ${sortColumn})、动态排序方向(ASC/DESC)。必须做严格白名单校验，如Set或Map验证传入值是否合法。
