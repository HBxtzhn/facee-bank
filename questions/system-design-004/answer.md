有了正确校验的签名之后，即使 JWT 被泄露或者截获，攻击者也无法在不知道签名密钥的情况下修改 Header 或 Payload 并生成有效签名。但签名不提供保密性，也不能阻止攻击者直接重放被盗的有效 JWT。

这是为什么呢？因为服务端拿到 JWT 之后，会解析出其中包含的 Header、Payload 以及 Signature 。服务端会根据 Header、Payload、密钥再次生成一个 Signature。拿新生成的 Signature 和 JWT 中的 Signature 作对比，如果一样就说明 Header 和 Payload 没有被修改。

不过，如果服务端的密钥也被泄露，攻击者就可以修改 Header 和 Payload，再重新生成一个有效的 Signature。

签名密钥必须妥善保管，并建立轮换和吊销机制。
