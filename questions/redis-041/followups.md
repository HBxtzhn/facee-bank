## 追问 1：Lua 脚本在集群模式下有什么限制？

脚本中操作的所有 key 必须在同一个 hash slot，否则返回 `CROSSSLOT` 错误。解决方案：① 使用 hash tag（如 `{tag}:key1` 和 `{tag}:key2`）强制同 slot；② 将多 key 操作拆分为单 key 操作；③ 使用 `redis.call` 时确保 key 路由正确。

## 追问 2：redis.call 和 redis.pcall 有什么区别？

`redis.call` 执行命令失败时抛出错误，中断脚本执行；`redis.pcall` 捕获错误并返回错误对象，脚本可继续执行。需要错误处理逻辑时用 `pcall`，严格模式用 `call`。

## 追问 3：Lua 脚本如何保证原子性？会不会阻塞其他请求？

Redis 单线程模型保证 Lua 脚本执行期间不会处理其他客户端命令，天然原子。但脚本执行时间过长会阻塞其他请求（Redis 有 `lua-time-limit` 默认 5s，超时后可通过 `SCRIPT KILL` 终止未调用写命令的脚本）。建议脚本保持短小，避免大量循环或大 key 操作。
