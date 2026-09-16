JWT（JSON Web Token）是 RFC 7519 定义的一种紧凑、URL 安全的声明表示格式。登录成功后，服务端可以把用户标识、权限范围和过期时间等声明写入 JWT；客户端在后续请求中携带它，服务端验证签名和相关声明后再决定是否放行。

JWT 可以承载鉴权所需的声明，因此服务端不一定要像传统 Session 方案那样保存会话状态。不过，撤销令牌、权限变更和主动下线等需求仍可能需要服务端状态，不能仅凭“使用 JWT”就认定系统完全无状态。

JWT 的 Header 和 Payload 只是经过 Base64Url 编码，拿到令牌的人都可以解码，不能把密码、身份证号等敏感信息写入 Payload。签名用于校验内容是否被篡改，并不负责加密内容。

如果客户端把 JWT 作为 Bearer Token 显式放入 `Authorization` Header，浏览器不会像 Cookie 那样自动附带它，因此可以降低传统 CSRF 风险。不过，这取决于凭据的传输和存储方式，而不是 JWT 格式本身；如果把 JWT 放在 Cookie 中，仍然需要 CSRF 防护。

JWT 优缺点分析详细介绍了使用 JWT 做身份认证的优势和限制。

下面是 RFC 7519 对 JWT 的定义。

> JSON Web Token (JWT) is a compact, URL-safe means of representing claims to be transferred between two parties. The claims in a JWT are encoded as a JSON object that is used as the payload of a JSON Web Signature (JWS) structure or as the plaintext of a JSON Web Encryption (JWE) structure, enabling the claims to be digitally signed or integrity protected with a Message Authentication Code (MAC) and/or encrypted. ——JSON Web Token (JWT)
