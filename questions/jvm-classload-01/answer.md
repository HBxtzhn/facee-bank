## 生命周期

加载 → 验证 → 准备 → 解析 → 初始化 → 使用 → 卸载。

- **准备**：为静态变量分配内存并设"零值"（`static int a = 1` 此时 a 为 0）；
- **解析**：符号引用 → 直接引用，可在初始化后延迟进行（动态绑定）；
- **初始化**：执行 `<clinit>`，即静态赋值与静态代码块，**按代码顺序**执行。

## 双亲委派

类加载请求先交给父加载器，父加载器无法完成才由自己加载：

```text
Bootstrap (rt.jar / java.base)
   ↑
Extension / Platform
   ↑
Application (classpath)
   ↑
自定义 ClassLoader
```

## 为什么需要

1. **安全**：用户无法用自定义的 `java.lang.String` 替换核心类（父加载器已加载，直接返回）；
2. **唯一性**：同一个类在同一个加载器命名空间内只被加载一次，避免类型混乱。

## 打破委派的场景

- **SPI**：`DriverManager` 需要加载 classpath 下的数据库驱动 ⇒ 线程上下文类加载器（TCCL）；
- **热部署**：Tomcat 每个 webapp 一个 `WebappClassLoader`，优先自己加载，实现应用隔离；
- **OSGi**：网络化依赖图，完全自定义规则。
