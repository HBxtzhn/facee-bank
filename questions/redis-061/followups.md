## 追问 1：如果字符串是 45 字节，Redis 会怎么分配内存？

45 字节超过 EMBSTR 阈值，使用 RAW 编码。`redisObject` 16 字节单独分配，SDS 使用 sdshdr8（头部 3 字节 + 45 字节数据 + 1 字节 `\0` = 49 字节），分配器会分配 64 字节给 SDS。总共 16 + 64 = 80 字节，且是两次独立分配。

## 追问 2：EMBSTR 为什么不能修改？

EMBSTR 将 redisObject 和 SDS 合并为一块连续内存，没有预留 `free` 空间。如果修改（如追加内容），需要重新分配更大的内存块并移动数据，不如直接用 RAW 编码的 SDS（有预分配空间）。因此 Redis 对 EMBSTR 执行修改操作时会自动转换为 RAW 编码。

## 追问 3：redisObject 的 16 字节具体包含哪些字段？

`redisObject` 包含：`type`（4字节，数据类型如 string/list/set）、`encoding`（4字节，编码方式如 embstr/raw/int）、`encoding_ptr`（8字节，指向实际数据的指针，64位系统）。共 16 字节。另外 `lru` 字段在某些版本中占 4 字节但通常不计入对齐计算。
