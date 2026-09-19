JVM参数分为标准参数和非标准参数（-XX开头），常用的按类别整理如下：

## 一、堆内存设置

|参数|作用|示例|
|---|---|---|
|`-Xms`|初始堆大小|`-Xms2g`|
|`-Xmx`|最大堆大小|`-Xmx4g`|
|`-Xmn`|新生代大小|`-Xmn1g`|
|`-XX:NewRatio`|老年代/新生代比例，默认2|`-XX:NewRatio=3`|
|`-XX:SurvivorRatio`|Eden/Survivor比例，默认8|`-XX:SurvivorRatio=8`|
|`-XX:MaxTenuringThreshold`|晋升老年代年龄阈值，默认15|`-XX:MaxTenuringThreshold=10`|
|`-XX:PretenureSizeThreshold`|大对象直接进老年代阈值|`-XX:PretenureSizeThreshold=1m`|

## 二、方法区/元空间

|参数|作用|
|---|---|
|`-XX:MetaspaceSize`|元空间初始大小|
|`-XX:MaxMetaspaceSize`|元空间最大大小|
|`-XX:PermSize` / `-XX:MaxPermSize`|永久代大小（Java7及之前）|

## 三、栈设置

|参数|作用|
|---|---|
|`-Xss`|每个线程栈大小，默认一般1M|
|`-XX:ThreadStackSize`|同-Xss|

## 四、垃圾收集器选择

|参数|作用|
|---|---|
|`-XX:+UseSerialGC`|Serial + Serial Old|
|`-XX:+UseParNewGC`|ParNew + Serial Old|
|`-XX:+UseConcMarkSweepGC`|ParNew + CMS|
|`-XX:+UseParallelGC`|Parallel Scavenge + Parallel Old|
|`-XX:+UseG1GC`|G1收集器|
|`-XX:+UseZGC`|ZGC收集器|

## 五、GC日志和监控

|参数|作用|
|---|---|
|`-XX:+PrintGCDetails`|打印GC详细信息|
|`-XX:+PrintGCDateStamps`|打印GC时间戳|
|`-Xloggc:gc.log`|GC日志输出到文件|
|`-XX:+PrintGCCause`|打印GC触发原因|
|`-XX:+PrintPromotionFailure`|打印晋升失败信息|
|`-XX:+PrintHeapAtGC`|GC前后打印堆信息|

## 六、CMS相关参数

|参数|作用|
|---|---|
|`-XX:CMSInitiatingOccupancyFraction`|CMS触发阈值，默认约92%|
|`-XX:+UseCMSCompactAtFullCollection`|Full GC时压缩内存|
|`-XX:CMSFullGCsBeforeCompaction`|多少次Full GC后压缩一次|
|`-XX:+CMSParallelRemarkEnabled`|并行最终标记|
|`-XX:+CMSScavengeBeforeRemark`|Remark前先做一次Young GC|

## 七、G1相关参数

|参数|作用|
|---|---|
|`-XX:MaxGCPauseMillis`|目标停顿时间，默认200ms|
|`-XX:G1HeapRegionSize`|Region大小，1~32MB|
|`-XX:InitiatingHeapOccupancyPercent`|触发并发标记的堆占用比例，默认45%|
|`-XX:G1MixedGCCountTarget`|Mixed GC次数，默认8|

## 八、性能优化参数

|参数|作用|
|---|---|
|`-XX:+DoEscapeAnalysis`|开启逃逸分析（默认开）|
|`-XX:+EliminateAllocations`|标量替换（默认开）|
|`-XX:+EliminateLocks`|锁消除（默认开）|
|`-XX:+UseBiasedLocking`|偏向锁|
|`-XX:+UseTLAB`|TLAB线程本地分配（默认开）|
|`-XX:+DisableExplicitGC`|禁止System.gc()|
|`-XX:+HeapDumpOnOutOfMemoryError`|OOM时自动dump堆|
|`-XX:HeapDumpPath`|dump文件路径|
