## 追问 1：G1也是用卡表吗？为什么G1用RSet？

答：G1不只用卡表，G1用的是**RSet（Remembered Set，记忆集）**，卡表是记忆集的一种具体实现。

传统分代只有新生代到老年代单向的跨代引用问题，卡表就够了。但G1是Region化的，Region之间互相引用，方向是任意的——A Region引用B Region，B Region也可能引用A Region。

所以G1每个Region都有自己的RSet，记录哪些外部Region引用了我这个Region里的对象。回收某个Region时，只需要扫它的RSet就能找到所有外部引用，不用扫整个堆。

RSet比卡表更灵活，但空间开销也更大。可以理解为：卡表是"我引用了谁"，RSet是"谁引用了我"。
