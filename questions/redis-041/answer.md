- **Lua 脚本**：Redis 内嵌 Lua 5.1 解释器，允许客户端通过 `EVAL` 或 `EVALSHA` 执行 Lua 脚本。脚本在 Redis 中以原子方式执行，执行期间不会处理其他客户端的命令。
- **使用方式**：
  - `EVAL script numkeys key [key ...] arg [arg ...]`：直接执行 Lua 脚本。
  - `EVALSHA sha1 numkeys key [key ...] arg [arg ...]`：通过脚本的 SHA1 摘要执行已缓存的脚本，避免重复传输脚本内容。
  - `SCRIPT LOAD script`：预加载脚本到缓存。
  - `SCRIPT EXISTS sha1 [sha1 ...]`：检查脚本是否已缓存。
  - `SCRIPT FLUSH`：清空脚本缓存。
- **脚本中调用 Redis 命令**：使用 `redis.call('COMMAND', arg1, arg2)` 或 `redis.pcall('COMMAND', arg1, arg2)`（后者捕获错误而非中断）。

## 扩展知识

- **原子性保证**：Lua 脚本执行期间，Redis 不会处理其他客户端的命令（单线程模型保证）。但脚本应尽量短小，避免长时间阻塞。
- **脚本限制**：
  - 不能使用全局变量（避免不同脚本间的状态干扰）。
  - 不能调用影响 Redis 状态的函数（如 `TIME` 在复制/持久化中需特殊处理）。
  - 返回的 key 必须通过参数传入（`KEYS` 数组），不能硬编码，以保证集群模式下的正确路由。
- **集群模式**：脚本中操作的所有 key 必须在同一 slot，否则返回 `CROSSSLOT` 错误。可通过 hash tag 保证。
- **脚本缓存**：Redis 会缓存执行过的脚本（LRU 淘汰），`EVALSHA` 通过 SHA1 摘要查找缓存，避免重复传输和解析脚本。

## 对比表格

| 方式 | 命令 | 优点 | 缺点 |
|------|------|------|------|
| EVAL | `EVAL script ...` | 简单直接 | 每次传输完整脚本，网络开销大 |
| EVALSHA | `EVALSHA sha1 ...` | 只传摘要，节省带宽 | 需先 SCRIPT LOAD 或 EVAL 缓存 |
| 事务 | `MULTI/EXEC` | 语法简单 | 不支持条件判断和错误处理 |
| Pipeline | 批量命令 | 减少网络往返 | 非原子，中间可能插入其他命令 |
