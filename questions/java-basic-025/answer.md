如果业务规定人民币金额统一精确到分，那么 `19.99` 元可以保存为 `1999` 分。加减法都在整数上完成，不会产生小数误差。

```java
long priceCents = 1_999L;
long shippingCents = 500L;
long totalCents = Math.addExact(priceCents, shippingCents);
```

`long` 适合订单金额、账户余额、支付金额这类已经完成舍入的值，数据库中可以使用 `BIGINT`。

数据库存储的时候，字段名最好带上单位，这样看着更直观一些：

```sql
CREATE TABLE orders (
    id           BIGINT PRIMARY KEY,
    amount_cents BIGINT NOT NULL
);
```

如果用 `amount` 的话，`amount = 100` 到底表示 100 元还是 100 分，只看数值无法判断。换成 `amount_cents = 100`，就不一样了。

**使用 long 时要注意什么？**

把金额统一存成分，精度也被固定在了两位小数。汇率、利息、税费或者按量计费的中间结果可能需要四位、六位甚至更多小数，这些计算不能继续拿“分”硬算。

还要防止溢出。普通的 `+` 和 `*` 在溢出后不会报错，金额代码可以改用 `Math.addExact()`、`Math.subtractExact()` 和 `Math.multiplyExact()`：

```java
long subtotalCents = Math.multiplyExact(unitPriceCents, quantity);
long balanceCents = Math.subtractExact(currentBalanceCents, paymentCents);
```

乘法还要检查中间结果。最终金额没有超过 `Long.MAX_VALUE`，不代表 `单价 × 数量 × 倍率` 的某一步也不会溢出。

多币种系统也不能假设所有货币都有两位小数。金额至少要和币种一起出现，最小单位位数由币种或业务规则决定，不能从一个孤立的 `long` 值中推断出来。
