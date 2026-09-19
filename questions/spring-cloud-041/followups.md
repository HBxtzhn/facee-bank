## 追问 1：Sentinel 的滑动窗口是怎么实现的？

Sentinel 用 LeapArray 实现滑动窗口。底层是一个固定长度的数组（默认2个元素，每个代表500ms），每个元素是一个 WindowWrap（包含 Bucket 和开始时间）。当前时间到来时，通过 CAS 操作更新对应的 Bucket。查询最近1秒的数据时，合并当前 Bucket 和前一个 Bucket 的数据。这样既保证了统计的精确性，又避免了频繁创建和销毁对象。

## 追问 2：Sentinel 的限流是单机还是分布式的？

默认是单机限流，每个 Sentinel 实例独立统计和限流。集群限流需要 Token Server——一个中心化的令牌服务器，各 Sentinel 实例向 Token Server 申请令牌，Token Server 控制整个集群的总 QPS。Token Server 可以是独立部署的，也可以嵌入在某个应用实例中。

## 追问 3：Sentinel 的性能怎么样？对业务有影响吗？

Sentinel 的性能很好，核心数据结构（LeapArray）用 CAS 无锁实现，没有线程竞争。统计和限流判断在内存中完成，不涉及 IO。官方测试单机 QPS 可以达到 25 万以上。对业务的影响很小（一般 1%-3% 的性能损耗）。但要注意资源数量不要太多（每个资源都有独立的统计数据），一般控制在几千个以内。
