# 理解 OIDC 流程

## 理解 OIDC 流程

本文为读者讲述 OIDC 认证协议的几种认证模式，包括授权码模式、隐式模式、混合模式；还有 OIDC 授权在业务中的触发、处理方式。配有流程图辅助理解。

### 什么时候需要认证？

当你的用户需要访问服务器上受保护资源的时候，例如用户订单数据、用户购物车，需要携带 id\_token。

id\_token 是用户的身份标识，就像身份证一样。id\_token 是一个 [JWT Token](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32)，由三部分组成，aaa.bbb.ccc。其中 aaa 是元信息，bbb 是用户信息，ccc 是签名。aaa、bbb 都是 JSON 对象经过 [base64](https://baike.baidu.com/item/base64/8545775) 编码后的字符。ccc 是签名二进制序列通过 base64 编码后的值。

**JWT Token 的三部分**

元信息：有一些属性，用于描述本 id\_token JWT 的签名算法，负载（payload）部分的类型等。

负载（payload）：里面会有一些属性，用于描述被认证者的身份和本 token 的信息，比如用户 id，颁发时间，接收方等。

签名：aaa 和 bbb 部分通过 `.` 连接，加上一个私钥进行摘要计算从而得到。如果 token 被篡改过，比如改变认证主体。那么服务器在本地计算的签名和 token 的 ccc 部分携带的签名就会不一致，从而拒绝请求。

![&#x6570;&#x636E;&#x4FDD;&#x62A4;&#x4E2D;&#x95F4;&#x4EF6;](../../.gitbook/assets/image%20%28145%29.png)

你应该在服务器上实现一个数据保护中间件，对特定的数据接口启用，如果请求没有携带 id\_token 或者签名校验失败就将用户重定向到登录页，如果 id\_token 校验成功，就返回数据给用户。

### 什么时候需要授权？

你为其他人提供身份服务，其他应用需要从你的服务器获取用户信息。比如 Github 提供身份服务，其他应用可以向 Github 索要用户的信息，完成用户在他们平台的注册，即三方登录。

第三方登录的实质是，让用户同意其他应用访问他在你的服务器上的信息，然后你会生成一个 Access Token 给第三方，只要第三方拥有这个 token，他就能代表用户访问此人在你的服务器上的数据。

### OIDC 流程

既然需要认证、授权，那么你的服务器、三方服务器、用户这三者如何通信呢？如何颁发 token？要遵守怎样的规范呢？OIDC 是一套认证授权的规范，应用广泛，轻量简单，基于 OAuth 2.0，既可以用于授权，也可以用于身份认证。

access\_token 用于**授权**，id\_token 用于**认证**。

#### 授权码模式

授权码模式是最常用的 OIDC 流程。 

![](../../.gitbook/assets/image%20%28203%29.png)

1. 三方应用发起授权请求（我需要访问这个用户在你的服务器上的数据！）
2. 你的服务器询问用户是否同意授权，要求用户输入用户名和密码，并弹出对方请求获取的信息条目（好的，我先问问用户是否同意你获取这些信息）
3. 返回一个授权码给三方应用前端（或后端）。（把这个授权码给你的后端，让他凭此来获取 token！）
4. 三方应用后端携带这个授权码向你服务器的 token 颁发接口请求数据。（请给我一个 token，授权码是 xxx）
5. 返回 id\_token access\_token。\(好的，这是你的 token，可以携带 access\_token 去用户信息接口获取数据\)

| 接口 | 授权码 | Access Token | ID Token |
| :--- | :--- | :--- | :--- |
| 授权 | 颁发 | X | X |
| Token | X | 颁发 | 颁发 |

scope 不带 openid 的情况

![](../../.gitbook/assets/image%20%28221%29.png)

可见最后没有返回 ID Token。

#### 隐式模式

当发起授权请求时 query 参数 response\_type=token 的时候，使用的是隐式模式。即使在 scope 中包含了 openid，ID Token 也不会被返回。隐式模式可以一次请求，立刻获取到 Access Token。

| 接口 | 授权码 | Access Token | ID Token |
| :--- | :--- | :--- | :--- |
| 授权 | X | 颁发 | X |

![](../../.gitbook/assets/image%20%28128%29.png)



当发起授权请求时 query 参数 response\_type=id\_token 的时候，使用的是隐式模式。只返回 ID Token。

| 接口 | 授权码 | Access Token | ID Token |
| :--- | :--- | :--- | :--- |
| 授权 | X | X | 颁发 |

![](../../.gitbook/assets/image%20%28104%29.png)



当发起授权请求时 query 参数 response\_type=id\_token token 的时候，使用的是隐式模式。同时返回 ID Token 和 Access Token。

| 接口 | 授权码 | Access Token | ID Token |
| :--- | :--- | :--- | :--- |
| 授权 | X | 颁发 | 颁发 |

![](../../.gitbook/assets/image%20%28200%29.png)



可以看出，隐式模式最为简单直接，但是在安全性上不如授权码模式，因为可能会在前端暴露 Access Token。隐式模式要求回调地址必须为 https。

#### 混合模式

混合模式的意思是，发起授权请求后，既返回授权码又返回 Access Token 或 ID Token。

当发起授权请求时 query 参数 response\_type=code id\_token 的时候，使用的是混合模式。

| 接口 | 授权码 | Access Token | ID Token |
| :--- | :--- | :--- | :--- |
| 授权 | 颁发 | X | 颁发 |
| Token | X | 颁发 | 颁发 |

![](../../.gitbook/assets/image%20%28193%29.png)



当发起授权请求时 query 参数 response\_type=code token 的时候，使用的是混合模式。如果 scope 中存在 openid，各类信息返回情况如下表：

| 接口 | 授权码 | Access Token | ID Token |
| :--- | :--- | :--- | :--- |
| 授权 | 颁发 | 颁发 | X |
| Token | X | 颁发 | 颁发 |

![](../../.gitbook/assets/image%20%28171%29.png)



如果 scope 不包含 openid，各类信息返回情况如下表：

| 接口 | 授权码 | Access Token | ID Token |
| :--- | :--- | :--- | :--- |
| 授权 | 颁发 | 颁发 | X |
| Token | X | 颁发 | 颁发 |

![](../../.gitbook/assets/image%20%28178%29.png)



当发起授权请求时 query 参数 response\_type=code id\_token token 的时候，使用的是混合模式。各类信息返回情况如下表：

| 接口 | 授权码 | Access Token | ID Token |
| :--- | :--- | :--- | :--- |
| 授权 | 颁发 | 颁发 | 颁发 |
| Token | X | 颁发 | 颁发 |

![](../../.gitbook/assets/image%20%28141%29.png)

这个用法可以一次性获取全部内容。

参考资料

[OIDC 的所有模式](https://medium.com/@darutk/diagrams-of-all-the-openid-connect-flows-6968e3990660)

