**CSRF(Cross Site Request Forgery)** 一般被翻译为 **跨站请求伪造** 。那么什么是 **跨站请求伪造** 呢？说简单点，就是用你的身份去发送一些对你不友好的请求。举个简单的例子：

小壮登录了某网上银行，他来到了网上银行的帖子区，看到一个帖子下面有一个链接写着“科学理财，年盈利率过万”，小壮好奇的点开了这个链接，结果发现自己的账户少了 10000 元。这是这么回事呢？原来黑客在链接中藏了一个请求，这个请求直接利用小壮的身份给银行发送了一个转账请求,也就是通过你的 Cookie 向银行发出请求。

```html
<a href="http://www.mybank.com/Transfer?bankId=11&money=10000"
  >科学理财，年盈利率过万</a

```

上面也提到过，进行 `Session` 认证的时候，我们一般使用 `Cookie` 来存储 `SessionId`。浏览器登录以后会自动在符合 Cookie 作用域的请求中带上它，服务端通过 `SessionId` 识别用户。攻击者如果直接窃取了 `SessionId`，造成的是会话劫持；而 CSRF 通常不要求攻击者读取 Cookie，它利用的是浏览器会自动携带 Cookie 的特性。

`Session` 认证中 `Cookie` 中的 `SessionId` 是由浏览器发送到服务端的，借助这个特性，攻击者就可以通过让用户误点攻击链接，达到攻击效果。

如果客户端把 Token 作为 Bearer Token，显式放入 `Authorization` Header，浏览器不会像 Cookie 那样自动把它附带到跨站请求中，因此可以降低传统 CSRF 风险。这里起作用的是凭据的携带方式，而不是 Token 或 JWT 这种格式本身。

不要因此默认把 Token 存入 `localStorage` 或 `sessionStorage`。同源页面中的恶意脚本可以读取 Web Storage，一处 XSS 漏洞就可能直接泄露 Token。浏览器应用可以根据场景选择 Backend For Frontend（BFF），或者使用设置了 `HttpOnly`、`Secure` 和合适 `SameSite` 属性的 Cookie；使用 Cookie 时还应结合 CSRF Token、`Origin`/`Referer` 校验等机制。

需要注意的是：不论是 `Cookie` 还是 `Token`，认证机制本身都无法避免 **跨站脚本攻击（Cross Site Scripting）XSS**。`HttpOnly` 可以降低脚本直接读取 Cookie 的风险，但 XSS 仍可能以用户身份发起请求，因此还需要正确的输出编码、必要时的 HTML 净化以及 CSP 等纵深防御。

> 跨站脚本攻击（Cross Site Scripting）缩写为 CSS 但这会与层叠样式表（Cascading Style Sheets，CSS）的缩写混淆。因此，有人将跨站脚本攻击缩写为 XSS。

XSS 中攻击者会用各种方式将恶意代码注入到其他用户的页面中。就可以通过脚本盗用信息比如 `Cookie` 。

推荐阅读：如何防止 CSRF 攻击？—美团技术团队

安全实践还可以参考：

- OWASP Session Management Cheat Sheet
- OWASP Cross-Site Request Forgery Prevention Cheat Sheet
