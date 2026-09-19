- **UV（Unique Visitor）**：统计一定时间范围内访问的去重用户数。
- **方案对比**：
  1. **SET**：`SADD uv:20260723 userId`，用 `SCARD` 获取总数。精确但内存占用大。
  2. **HyperLogLog**（推荐）：`PFADD uv:20260723 userId`，`PFCOUNT uv:20260723` 获取估算值。每个 key 仅占 12KB，可统计最多 2^64 个元素，标准误差 0.81%。
  3. **Bitmap**：`SETBIT uv:20260723 userId 1`，用 `BITCOUNT` 统计。适合 userId 连续且范围可控的场景。
- **推荐方案**：HyperLogLog，在大数据量下内存效率远超 SET，误差可接受。

## 扩展知识

**HyperLogLog 原理**：
- 基于"桶中最大前导零个数"的统计原理（概率论中的基数估计）
- 内部维护 16384 个桶（register），每个桶记录对应哈希值的前导零最大位数
- 通过调和平均数和修正公式估算基数
- Redis 的 HyperLogLog 在元素较少时使用稀疏矩阵编码（几KB），元素多时使用密集矩阵编码（固定12KB）

**合并统计（多日 UV）**：
- `PFMERGE dest key1 key2 ...`：将多个 HyperLogLog 合并，可统计多日总 UV
- 注意：合并后无法区分每日 UV

**Bitmap 方案的 userId 映射**：
- 如果 userId 是递增整数（如 1~10亿），直接用 userId 作为 offset
- 如果 userId 是 UUID 等非连续值，需要先映射为连续整数（维护一张映射表）

## 对比表格

| 方案 | 内存占用 | 精确度 | 适用场景 | 最大支持量 |
|------|---------|--------|---------|-----------|
| SET | O(N)，每个元素约几十字节 | 100% 精确 | 小数据量、需要精确值 | 受内存限制 |
| HyperLogLog | 固定 12KB | 误差 0.81% | 大数据量 UV 统计 | 2^64 |
| Bitmap | O(max_id/8) | 100% 精确 | userId 连续且范围可控 | 受 offset 范围限制 |
