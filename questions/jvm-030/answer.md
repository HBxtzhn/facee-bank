**会，方法区（元空间）一样会OOM。**

## Java7及之前：永久代OOM

- 方法区的实现叫永久代（Permanent Generation）

- 有固定大小上限（`-XX:PermSize` 和 `-XX:MaxPermSize`）

- 加载的类、常量太多填满永久代后，抛出 `java.lang.OutOfMemoryError: PermGen space`

- 典型场景：部署了大量应用的Tomcat、动态生成大量代理类的框架（Spring、CGLIB）

## Java8及之后：元空间OOM

- 永久代被移除，方法区改为元空间（Metaspace）实现

- 元空间使用**本地内存（Native Memory）**，不再占用堆内存

- 默认情况下元空间可以动态扩容，只受物理内存限制

- 但如果设置了 `-XX:MaxMetaspaceSize` 上限，超出后会抛出 `java.lang.OutOfMemoryError: Metaspace`

- 即使不设上限，物理内存耗尽也会OOM

## 触发方法区OOM的常见场景

1. **动态生成大量类**：CGLIB动态代理、Javassist字节码增强、JSP动态编译

2. **热部署频繁**：不停重载类但旧的类加载器没卸载，类元数据累积

3. **应用太多**：一个容器部署几十个应用，加载的类总量巨大

4. **常量过多**：运行时常量池里的字符串常量、静态常量太多
