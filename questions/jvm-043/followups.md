## 追问 1：对象什么时候从新生代晋升到老年代？

答：有几种情况：

1. **年龄达标**：对象每熬过一次Young GC，年龄+1，默认达到15岁（-XX:MaxTenuringThreshold）就晋升老年代

2. **动态年龄判定**：Survivor区中相同年龄的所有对象大小总和超过Survivor空间的一半，年龄大于等于这个年龄的对象直接进老年代，不用等15岁

3. **大对象直接进入**：超过-XX:PretenureSizeThreshold阈值的大对象，直接在老年代分配，避免在Eden和Survivor之间来回复制

4. **分配担保失败**：Young GC后Survivor放不下存活对象，通过分配担保机制直接转移到老年代
