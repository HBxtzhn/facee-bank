## 对比

| 回收器 | 分代 | 停顿特点 | 适用场景 |
|---|---|---|---|
| Serial | 是 | 单线程 STW | 客户端、小堆 |
| ParNew / Parallel Scavenge | 是 | 多线程 STW，吞吐优先 | 批处理、离线任务 |
| CMS | 是 | 并发标记清除，停顿短但有碎片 | 已淘汰（JDK 9 弃用，14 移除） |
| G1 | 是（逻辑分代） | Region 化，可设 `-XX:MaxGCPauseMillis` | **通用默认**，大堆低延迟 |
| ZGC / Shenandoah | 否 | 并发整理，亚毫秒级停顿 | 超大堆、极低延迟 |

## 线上选型

**选 G1**，理由：

1. 堆 8G 属于 G1 的舒适区（6–32G 常见），Region 化可预测停顿；
2. 目标可量化：`-XX:MaxGCPauseMillis=200`，让回收器自行平衡；
3. 提供 `-XX:+HeapDumpOnOutOfMemoryError` + GC 日志便于定位。

关键参数：

```bash
-XX:+UseG1GC -Xms8g -Xmx8g -XX:MaxGCPauseMillis=200
-XX:InitiatingHeapOccupancyPercent=45   # 触发并发标记的堆占用阈值
-Xlog:gc*:file=/var/log/gc.log:time,uptime:filecount=10,filesize=32m
```

注意：`-Xms` 与 `-Xmx` 设成相等，避免运行期堆伸缩带来的额外停顿；如果实测停顿仍不达标且堆 > 16G，再评估 ZGC。
