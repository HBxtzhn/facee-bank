折扣、税费、利息和汇率换算经常产生超过货币最小单位的中间结果。

`BigDecimal` 用任意精度整数和 `scale` 表示十进制数，能够把这些中间值保留下来，再在业务规定的位置舍入。

```java
BigDecimal price = new BigDecimal("19.99");
BigDecimal discountRate = new BigDecimal("0.95");

BigDecimal discountedPrice = price.multiply(discountRate);
// 18.9905
```

金额常量直接使用字符串构造。接口传过来的是字符串就直接转成 `BigDecimal`，数据库字段是 `DECIMAL` 就直接映射成 `BigDecimal`，中间不需要再转成 `double`。

**如果 `divide()` 除不尽的话，怎么办呢？**

这个时候需要指定保留位数和舍入方式。

下面的代码的意思就是保留两位小数，并使用 `HALF_UP`（四舍五入）。如果直接调用 `a.divide(b)`，程序会抛出 `ArithmeticException`。

```java
BigDecimal a = new BigDecimal("10");
BigDecimal b = new BigDecimal("3");

System.out.println(a.divide(b, 2, RoundingMode.HALF_UP)); // 3.33
```

还有一点需要注意：`BigDecimal` 是不可变类，运算结果要用新的变量接收，或者重新赋值。第一次调用 `add()` 时没有接收返回值，`amount` 还是 `10.00`：

```java
BigDecimal amount = new BigDecimal("10.00");

amount.add(new BigDecimal("2.00"));
System.out.println(amount); // 仍然是 10.00

amount = amount.add(new BigDecimal("2.00"));
System.out.println(amount); // 12.00
```

比较金额大小一般使用 `compareTo()`。`equals()` 还会比较 `scale`，所以 `1.0` 和 `1.00` 调用 `equals()` 的结果为 `false`：

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("1.00");

System.out.println(a.equals(b));         // false
System.out.println(a.compareTo(b) == 0); // true
```

这个差异也会影响 `HashMap` 和 `HashSet`。如果用 `BigDecimal` 作为键，最好先统一 `scale`，否则 `1.0` 和 `1.00` 会被当成两个键。
