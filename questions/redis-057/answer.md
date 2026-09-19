- **Geo 数据结构**：Redis 3.2 引入，用于存储和查询地理位置信息（经纬度）。
- **底层实现**：基于 ZSET（有序集合），将二维经纬度编码为一维整数（GeoHash），作为 score 存储。
- **核心命令**：
  - `GEOADD key longitude latitude member`：添加地理位置
  - `GEODIST key member1 member2 [m|km|ft|mi]`：计算两点距离
  - `GEORADIUS key longitude latitude radius m|km`：以指定坐标为圆心，查询半径内的成员
  - `GEOSEARCH key FROMLONLAT lon lat BYRADIUS radius m|km`：Redis 6.2+ 推荐命令
  - `GEOPOS key member`：获取成员的经纬度
  - `GEOHASH key member`：获取成员的 GeoHash 字符串
- **典型应用**：附近的人、外卖配送范围、共享单车定位。

## 扩展知识

**GeoHash 编码原理**：
- 将地球经纬度范围分别进行二分编码，经度和纬度交替取位，组成一个 52 位整数
- 经度范围 [-180, 180]，纬度范围 [-85.05, 85.05]（Web Mercator 投影限制）
- GeoHash 值越接近，实际地理位置越近
- 52 位精度约对应 0.6 米分辨率

**GEORADIUS vs GEOSEARCH**：
- `GEORADIUS` 在 Redis 6.2 被标记为废弃，推荐使用 `GEOSEARCH`
- `GEOSEARCH` 支持按矩形范围查询（`BYBOX`），更灵活
- `GEOSEARCHSTORE` 可将结果存储到新 key

**Geo 的精度限制**：
- GeoHash 编码精度有限（约 0.6m），极端接近的点可能编码相同
- 不支持极地地区（纬度 > 85.05°）

## 对比表格

| 命令 | 功能 | 版本要求 | 备注 |
|------|------|---------|------|
| GEOADD | 添加位置 | 3.2+ | 底层是 ZADD |
| GEODIST | 计算距离 | 3.2+ | 返回米/千米/英里 |
| GEORADIUS | 范围查询 | 3.2+ | 已废弃（6.2+） |
| GEOSEARCH | 范围查询 | 6.2+ | 支持圆形和矩形 |
| GEOPOS | 获取坐标 | 3.2+ | 返回经纬度 |
| GEOHASH | 获取 GeoHash | 3.2+ | 返回 11 位字符串 |
