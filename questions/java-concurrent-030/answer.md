| 维度 | synchronized | ReentrantLock |
|------|-------------|---------------|
| 实现 | JVM关键字，底层monitor | JDK API，AQS实现 |
| 锁释放 | 自动(代码块结束) | 必须finally中unlock |
| 中断响应 | 不可中断 | lockInterruptibly可中断 |
| 公平性 | 非公平 | 可选公平/非公平 |
| 尝试获取 | 不支持 | tryLock可尝试 |
| 条件 | wait/notify | 可绑定多个Condition |

synchronized适合简单同步，ReentrantLock适合需要超时、可中断、公平锁等高级特性。
