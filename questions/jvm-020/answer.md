Linux Load Average 统计运行中和不可中断睡眠的任务。磁盘、网络存储或某些内核 I/O 等待严重时，CPU 使用率可能不高，Load 仍会持续上升。

```bash
vmstat 1
iostat -xz 1
pidstat -d -p  1
```

重点观察 `vmstat` 中的运行队列、I/O 等待和阻塞任务，以及 `iostat` 中设备利用率、等待时间和队列。`iostat`、`pidstat` 通常由 sysstat 软件包提供，线上环境是否可用要提前确认。
