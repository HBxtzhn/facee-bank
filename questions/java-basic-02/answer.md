| 类型 | 可变性 | 线程安全 | 适用场景 |
|---|---|---|---|
| `String` | 不可变 | 安全（不可变天然安全） | 少量、不频繁修改的字符串 |
| `StringBuilder` | 可变 | 不安全 | 单线程拼接，**首选** |
| `StringBuffer` | 可变 | 方法上加 `synchronized` | 多线程共享同一实例的拼接 |

要点：

- `String` 不可变 ⇒ 每次拼接都产生新对象，循环内拼接会放大开销（编译器会把简单拼接优化为 `StringBuilder`，但**循环体内不会**）。
- `StringBuffer` 的同步粒度是整个方法，绝大多数场景是过度保护。
- 结论：默认用 `StringBuilder`；只有在确实多线程共享同一 builder 时才用 `StringBuffer`。
