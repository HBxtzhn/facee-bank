## 追问 1：Redis 的单线程模型和 Node.js 有什么异同？

相同：都是单线程事件循环模型，都使用 I/O 多路复用。不同：① Redis 是纯内存操作，Node.js 大量依赖异步 I/O 回调；② Redis 自行实现事件循环（ae），Node.js 基于 libuv；③ Redis 专注于 KV 操作，Node.js 是通用运行时。

## 追问 2：大 key 问题会导致什么后果？如何解决？

大 key 操作会阻塞主线程，导致其他请求超时。解决方案：① 使用 `UNLINK` 异步删除；② 使用 `SCAN` 替代 `KEYS`；③ 拆分大 key（如大 Hash 分片）；④ 设置合理的 `lazyfree-lazy-eviction` 等配置项；⑤ 监控大 key（`redis-cli --bigkeys` 或 `MEMORY USAGE`）。

## 追问 3：Redis 未来会完全多线程化吗？

不太可能。Redis 的核心优势之一是命令执行的原子性和简洁的实现。完全多线程化需要引入大量锁机制，增加复杂度和降低可靠性。当前 I/O 多线程已经解决了主要瓶颈，命令执行层保持单线程是合理的设计选择。
