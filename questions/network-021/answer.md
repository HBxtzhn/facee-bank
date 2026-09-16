客户端发送第一次 FIN 后进入 `FIN_WAIT_1`，并启动重传计时器。如果在超时时间内没有收到对端对 FIN 的确认 ACK，客户端会重传 FIN。

服务端如果收到重复 FIN，通常会再次发送 ACK。如果由于网络问题 ACK 一直无法送达，客户端在达到一定重试或超时阈值后，可能报错或放弃。具体行为受实现和参数影响：在 Linux 中，如果 socket 已经被应用关闭、成为 orphaned socket，后续重试更直接受 `tcp_orphan_retries` 影响；普通存活连接上的 RTO 重传超时则和 `tcp_retries2` 有关。
