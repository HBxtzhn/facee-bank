## 区别

| 比较项 | `==` | `equals()` |
|---|---|---|
| 类型 | 运算符 | 方法（`Object` 定义） |
| 基本类型 | 比较值 | 不适用 |
| 引用类型 | 比较地址 | 默认比较地址，可重写为比较内容 |

```java
String a = new String("abc");
String b = new String("abc");
System.out.println(a == b);      // false：两个不同对象
System.out.println(a.equals(b)); // true：String 重写了 equals
```

## 为什么必须同时重写 hashCode

散列集合（`HashMap`、`HashSet`）先用 `hashCode()` 定位桶，再用 `equals()` 在桶内比较。若两个对象 `equals` 为 true 但哈希值不同，它们会落进不同的桶，导致集合**查不到已存在的元素**，出现"放进去却取不出来"。

契约（`Object` 的 javadoc）：
1. `equals` 为 true ⇒ `hashCode` 必须相等；
2. `hashCode` 相等 ⇏ `equals` 为 true（允许哈希冲突）。
