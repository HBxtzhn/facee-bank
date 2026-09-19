## 追问 1：GraalVM的Native Image和传统JIT比，最大的 trade-off 是什么？

答：最大的trade-off是**用峰值性能和灵活性换启动速度和内存占用**。

Native Image用AOT编译出一个独立可执行文件，启动毫秒级、内存占用极小，非常适合云原生和Serverless。

但代价是：

1. 峰值性能不如C2编译的HotSpot，因为没有运行时profile优化

2. 不支持动态类加载、反射等动态特性，需要配置元数据

3. 构建时间很长，编译过程很慢

4. 调试和profile工具链不如HotSpot成熟

简单说：长期运行的服务端程序用HotSpot JIT更好；短生命周期、追求快速启动的用AOT Native Image更好。
