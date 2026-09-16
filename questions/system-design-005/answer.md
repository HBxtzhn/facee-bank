在基于 JWT 进行身份验证的应用程序中，服务器通过 Payload、Header 和密钥创建 JWT 并将 JWT 发送给客户端。客户端需要根据应用形态和威胁模型安全地保存令牌，以后发出的请求会携带这个令牌。

简化后的步骤如下：

1. 用户向服务器发送用户名、密码以及验证码用于登陆系统；
2. 如果用户用户名、密码以及验证码校验正确的话，服务端会返回已经签名的 Token，也就是 JWT；
3. 客户端收到 Token 后安全保存；浏览器应用可以使用 BFF 把令牌保留在服务端，或者根据场景使用受保护的 Cookie；
4. 用户以后每次向后端发请求都在 Header 中带上这个 JWT ；
5. 服务端检查 JWT 并从中获取用户相关信息。

两点建议：

1. 不要默认把 JWT 存放在 `localStorage` 或 `sessionStorage` 中。同源页面中的任意恶意脚本都能读取 Web Storage，一处 XSS 漏洞就可能泄露令牌。使用 Cookie 时，应设置 `HttpOnly`、`Secure` 和合适的 `SameSite` 属性，并同时做好 CSRF 防护。
2. 非 Cookie 方案携带 JWT 的常见做法是将其放在 HTTP Header 的 `Authorization` 字段中（`Authorization: Bearer Token`）。

**spring-security-jwt-guide** 就是一个基于 JWT 来做身份认证的简单案例，感兴趣的可以看看。
