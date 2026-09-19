## 追问 1：线上CPU飙高，你的排查步骤是什么？

答：标准排查流程：

1. `top` 命令找到CPU最高的Java进程，记下PID

2. `top -Hp PID` 看这个进程里哪个线程CPU最高，记下线程ID

3. `printf "%x\n" 线程ID` 把线程ID转成16进制

4. `jstack PID | grep -A 20 16进制线程号` 找到对应的线程栈

5. 看栈信息定位是哪个方法在消耗CPU，是死循环还是GC还是别的原因

如果是GC导致的CPU高，再用jstat看GC频率，jmap分析堆里是什么对象多，一步步往下查。

有Arthas的话更简单：`thread -n 3` 直接列出CPU最高的3个线程和栈。
