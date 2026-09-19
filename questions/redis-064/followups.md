## 追问 1：List 底层为什么用 QuickList 而不是单纯的 linkedlist 或 ziplist？

linkedlist 每个节点都需要额外的指针开销（prev/next），内存利用率低。ziplist 内存紧凑但查找/插入是 O(N)，且修改时可能触发连锁更新。QuickList 将两者结合：双向链表连接多个 ziplist，既保证了内存紧凑（ziplist），又保证了操作效率（双向链表 O(1) 定位到段，段内 ziplist 操作）。

## 追问 2：如何用 List 实现一个固定长度的最新消息列表？

使用 `LPUSH key message` 插入新消息，然后 `LTRIM key 0 99` 保留最新 100 条。或者合并为一步：`LPUSH` 后紧跟 `LTRIM`。也可以将两个命令放入 Pipeline 减少网络往返。

## 追问 3：BLPOP 的阻塞机制是怎样的？

客户端执行 `BLPOP key timeout` 时，如果 list 为空，连接进入阻塞状态等待。当其他客户端向该 list 推入数据时，服务端立即唤醒阻塞的客户端并返回数据。如果超时仍无数据，返回 nil。阻塞期间不消耗 CPU，但会占用一个连接。
