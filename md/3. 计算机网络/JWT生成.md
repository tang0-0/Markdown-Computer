# JWT的认识

## 什么是JWT

JWT全名Json Web Token是一种基于json的token格式，它遵循开源标准[RFC 7519](https://www.rfc-editor.org/rfc/rfc7519.html)。

## JWT的用途

1. 身份认证：这是使用JWT的最常见场景。一旦用户登录，后续每个请求都将包含JWT，允许用户访问该令牌允许的路由、服务和资源。单点登录是现在广泛使用的JWT的一个特性，因为它的开销很小，并且可以轻松地跨域使用。
2. 信息交换：对于安全的在各方之间传输信息而言，JSON Web Tokens无疑是一种很好的方式。因为JWT可以被签名，例如，用公钥/私钥对，你可以确定发送人就是它们所说的那个人。另外，由于签名是使用头和有效负载计算的，您还可以验证内容没有被篡改。

对应这两种用途JWT又分为两种格式

1. JWS:JSON Web Signature,适用于身份认证。[RFC 7517](https://www.rfc-editor.org/rfc/rfc7515)
2. JWE:JSON Web Encryption,适用于信息交换。

## JWS

JWS是由三段base64urlsafe编码的字符串组成`{Header}.{Payload}.{Signature}`
实例如下：
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

1. Header
2. Payload
3. Signature
