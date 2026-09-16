服务端不是 65535 上限，但客户端访问同一个目标时，临时端口可能先耗尽。

例如客户端固定为 `192.168.1.10`，不断连接 `10.0.0.1:443`。这时目的 IP、目的端口、源 IP 都固定，只剩源端口可变。源端口用完后，就无法再创建新四元组。

Linux 自动分配临时端口范围可以这样看：

```bash
sysctl net.ipv4.ip_local_port_range
```

Mac 下可以这样查看：

很多 Linux 环境默认临时端口范围是 `32768 60999`，大约 2.8 万个端口；实际值以 `sysctl net.ipv4.ip_local_port_range` 输出为准，且不是全部 `0~65535` 都会自动拿来做临时端口。

看到 `Cannot assign requested address` / `EADDRNOTAVAIL`、大量 `connect` 失败，且目标 `IP:Port` 很集中时，要怀疑临时端口耗尽或 `TIME_WAIT` 堆积。
