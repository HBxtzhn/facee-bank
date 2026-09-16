JWT 通常由三个使用 `.` 分隔的 Base64Url 编码部分组成：

- **Header（头部）**：描述 JWT 的元数据，包含令牌类型和签名算法。Header 被 Base64Url 编码后成为 JWT 的第一部分。
- **Payload（载荷）**：存放需要传递的声明（Claims），如 `sub`（subject，主题）、`jti`（JWT ID）。Payload 被 Base64Url 编码后成为 JWT 的第二部分。
- **Signature（签名）**：根据编码后的 Header、Payload、签名算法和签名密钥计算。HS256 使用共享密钥，RS256、ES256 等非对称算法使用私钥签名、公钥验证。

JWT 通常是这样的：`xxxxx.yyyyy.zzzzz`。

示例：

```plain
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

你可以在 jwt.io 对示例 JWT 进行解码，解码后可以看到 Header、Payload、Signature 这三部分。生产环境中的真实令牌可能包含用户标识和权限信息，不要复制到第三方在线工具中。

Header 和 Payload 都是 JSON 数据，Signature 则由编码后的 Header、Payload 和签名密钥计算得到。

### JWT 解析和 JWT 验证有什么区别？

JWT 解析只会对 Header 和 Payload 做 Base64Url 解码，不需要密钥。任何拿到令牌的人都能完成解析，因此解析结果不能证明令牌可信。

JWT 验证会使用指定算法和密钥校验 Signature，还应检查 `exp`、`nbf`、`iss`、`aud` 等声明。只有签名和业务要求的声明全部通过校验，服务端才能信任令牌中的身份与权限信息。

### Header

Header 通常由两部分组成：

- `typ`（Type）：令牌类型，也就是 JWT。
- `alg`（Algorithm）：签名算法，比如 HS256。

示例：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

JSON 形式的 Header 经 Base64Url 编码后成为 JWT 的第一部分。

### Payload

Payload 也是 JSON 数据，其中包含 Claims（声明）。

Claims 分为三种类型：

- **Registered Claims（注册声明）**：预定义的一些声明，建议使用，但不是强制性的。
- **Public Claims（公有声明）**：JWT 签发方可以自定义的声明，但是为了避免冲突，应该在 IANA JSON Web Token Registry 中定义它们。
- **Private Claims（私有声明）**：JWT 签发方因为项目需要而自定义的声明，更符合实际项目场景使用。

下面是一些常见的注册声明：

- `iss`（issuer）：JWT 签发方。
- `iat`（issued at time）：JWT 签发时间。
- `sub`（subject）：JWT 主题。
- `aud`（audience）：JWT 接收方。
- `exp`（expiration time）：JWT 的过期时间。
- `nbf`（not before time）：JWT 生效时间，早于该定义的时间的 JWT 不能被接受处理。
- `jti`（JWT ID）：JWT 唯一标识。

示例：

```json
{
  "uid": "ff1212f5-d8d1-4496-bf41-d2dda73de19a",
  "sub": "1234567890",
  "name": "John Doe",
  "exp": 15323232,
  "iat": 1516239022,
  "scope": ["admin", "user"]
}
```

Payload 部分默认是不加密的，**一定不要将隐私信息存放在 Payload 当中！！！**

JSON 形式的 Payload 经 Base64Url 编码后成为 JWT 的第二部分。

### Signature

Signature 部分是对前两部分的签名，作用是防止 JWT（主要是 payload） 被篡改。

这个签名的生成需要用到：

- Header + Payload。
- 存放在服务端的签名密钥。使用非对称算法时，签名私钥不能泄露。
- 签名算法。

签名的计算公式如下：

```plain
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

算出签名以后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用“点”（`.`）分隔，这个字符串就是 JWT。
