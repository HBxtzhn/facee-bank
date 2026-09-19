## 追问 1：抢票场景用synchronized还是ReentrantLock？

ReentrantLock。高并发下希望获取不到锁的快速失败返回"已满"而非一直阻塞。tryLock设超时(100ms)超时立刻返回友好提示。synchronized只能一直阻塞。

## 追问 2：synchronized锁升级过程？

无锁→偏向锁(同线程反复获取记录线程ID)→轻量级锁(另一个线程竞争CAS自旋)→重量级锁(自旋超10次或等待线程超CPU核一半，线程阻塞由OS调度)。
