1. **核心原理**：Sentinel 基于滑动窗口算法实现限流。在内存中维护一个滑动窗口，统计最近一段时间内的请求数据（通过数、被拒数、响应时间等），根据统计结果判断是否触发限流。

2. **资源定义**：通过 @SentinelResource 注解或 SphU.entry("resourceName") 定义资源（需要保护的代码块）。每个资源有独立的统计数据和控制规则。

3. **限流规则**：通过 FlowRule 定义限流规则——资源名、阈值类型（QPS/线程数）、阈值、限流策略（直接拒绝、Warm Up 预热、匀速排队）。

4. **统计结构**：Sentinel 的滑动窗口把时间分成多个 Bucket（默认每秒2个 Bucket，每个 Bucket 500ms）。每个 Bucket 记录通过数（passCount）、被拒数（blockCount）、异常数（exceptionCount）、响应时间（rt）。查询时合并最近 N 个 Bucket 的数据。

5. **限流执行流程**：请求进入 → 查找资源对应的限流规则 → 从滑动窗口获取统计数据 → 判断是否超过阈值 → 超过则拒绝（走 blockHandler），未超过则放行。

## 扩展知识

- **源码关键类**：FlowRuleChecker → TrafficShapingController（具体限流逻辑）→ StatisticNode（统计数据）→ LeapArray（滑动窗口实现）。LeapArray 是 Sentinel 的核心数据结构，用数组 + CAS 实现无锁的滑动窗口。
- **匀速排队（Rate Limiter）**：基于漏桶算法，请求按固定间隔通过。使用虚拟队列（PriorityQueue）实现，请求按到达时间排序，逐个处理。适合需要匀速处理的场景（比如消息消费）。
- **Warm Up（预热）**：冷启动模式，限流阈值从小值逐渐增长到设定值。适合系统刚启动时预热（比如数据库连接池还没建立、JIT 编译还没完成）。
