## 追问 1：Geo 底层为什么用 ZSET 实现？

GeoHash 将二维经纬度编码为一维整数，天然具有有序性。用 ZSET 的 score 存储 GeoHash 值，可以利用跳表的有序性快速进行范围查询（GEORADIUS 本质上是 ZRANGEBYSCORE）。同时复用 ZSET 的所有命令和操作能力。

## 追问 2：GEORADIUS 的查询复杂度是多少？

时间复杂度 O(N+log(M))，其中 M 是元素总数，N 是范围内的元素数。先通过跳表定位到范围起点（O(logM)），然后顺序遍历范围内元素（O(N)）。

## 追问 3：如何实现"附近的人并按距离排序"？

使用 `GEOSEARCH key FROMLONLAT lon lat BYRADIUS radius km ASC COUNT 20`，`ASC` 按距离升序排列，`COUNT 20` 限制返回前 20 个。底层会计算每个成员与目标点的距离并排序。
