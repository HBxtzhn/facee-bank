如果我们需要保存密码这类敏感数据到数据库，需要先通过自适应单向哈希函数编码再保存，而不是使用可逆加密。

Spring Security 提供了多种密码编码算法的实现，开箱即用。这些实现类的接口是 `PasswordEncoder`；如果需要自定义密码编码方案，也需要实现 `PasswordEncoder` 接口。

`PasswordEncoder` 接口有 `encode()` 和 `matches()` 两个必须实现的抽象方法，以及一个可以按需覆盖的默认方法 `upgradeEncoding()`。

```java
public interface PasswordEncoder {
    // 对原始密码进行单向编码
    String encode(CharSequence var1);
    // 比对原始密码和数据库中保存的密码
    boolean matches(CharSequence var1, String var2);
    // 判断已编码密码是否需要升级编码，默认返回 false
    default boolean upgradeEncoding(String encodedPassword) {
        return false;
    }
}
```

官方推荐使用可调节工作因子的自适应单向函数，并根据系统性能调优验证耗时，例如 bcrypt、PBKDF2、scrypt 或 Argon2。
