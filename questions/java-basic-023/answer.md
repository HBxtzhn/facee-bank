静态方法在静态上下文中执行，没有隐式的当前实例 `this`，因此不能直接访问实例成员。静态方法仍然可以通过一个显式的对象引用访问该对象的实例成员，这与类加载或成员是否已经“分配内存”无关。

```java
public class Example {
    // 定义一个字符型常量
    public static final char LETTER_A = 'A';

    // 定义一个字符串常量
    public static final String GREETING_MESSAGE = "Hello, world!";

    public static void main(String[] args) {
        // 输出字符型常量的值
        System.out.println("字符型常量的值为：" + LETTER_A);

        // 输出字符串常量的值
        System.out.println("字符串常量的值为：" + GREETING_MESSAGE);
    }
}
```
