# JWT Token 释义、使用及验证

Authing 默认基于 JWT Token 进行身份认证。

## JWT 简介

Json web token \(JWT\)，是为了在网络应用环境间传递声明而执行的一种基于 JSON 的开放标准（\(RFC 7519\)。该 token 被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT 的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该 token 也可直接被用于认证，也可被加密。

详细内容可以参考这篇文章：[什么是 JWT](https://www.jianshu.com/p/576dbf44b2ae)。

## 认证流程

![auth\_uml](https://usercontents.authing.cn/white_paper/authing_auth_uml.png)

### 用户认证的流程

* 用户使用账号（手机/邮箱/用户名）密码请求服务器
* 服务器验证用户账号是否和数据库匹配
* 服务器通过验证后发送给客户端一个 JWT Token
* **客户端存储 Token，并在每次请求时携带该 Token（**[**如何携带？**](https://learn.authing.cn/authing/advanced/authentication/jwt-token#ke-hu-duan-fu-dai-jwt-token-de-fang-shi)**）**
* **服务端验证 Token 值，并根据 Token 合法性返回对应资源（**[**如何验证？**](https://learn.authing.cn/authing/advanced/authentication/jwt-token#ru-he-yan-zheng-token-he-fa-xing)**）**

加粗部分为开发者需要进行的工作。

## 安全限制

为防止用户恶意注册，系统对 IP 默认进行了限制，条件如下：

* 单 IP `5 分钟`内连续发起注册请求超过 `3 次`会被禁止；
* 单 IP `5 分钟`内连续发起登录请求超过 `3 次`会要求输入验证码；

若想开启/关闭或修改此限制，请参考：[开启/关闭/配置注册频率限制](https://learn.authing.cn/authing/quickstart/dashboard#kai-qi-guan-bi-pei-zhi-zhu-ce-pin-lv-xian-zhi)。

## 客户端附带 JWT Token 的方式

用户在完成认证后会返回开发者一个 JWT Token，开发者需将此 Token 存储于客户端，然后将此 Token 发送给开发者受限的后端服务器进行验证。

建议使用 **HTTP Header Authorization** 的形式携带 Token，以下以 JavaScript 的 axios 库为例示范如何携带：

```javascript
const axios = require('axios');
axios.get({
  url: 'https://yourdomain.com/api/v1/your/resources',
  headers: {
    'Authorization': 'Bearer YOUR_JWT_TOKN'
  }
}).then((res) => {
 // custom codes
})
```

注意第五行前面有 **Bearer 类型。**

### **Bearer 是什么意思？**

Bearer Token \([RFC 6750](http://www.rfcreader.com/#rfc6750)\) 用于授权访问资源，任何 Bearer 持有者都可以无差别地用它来访问相关的资源，而无需证明持有加密 key。一个 Bearer 代表授权范围、有效期，以及其他授权事项；一个 Bearer 在存储和传输过程中应当防止泄露，需实现 Transport Layer Security \(TLS\)；一个 Bearer 有效期不能过长，过期后可用 Refresh Token 申请更新。

建议开发者遵循规范，在每次请求的 Token 前附带 Bearer。

## 验证 Token 合法性

你可以在任意客户端将 JWT Token 发送给后端验证该 JWT 的合法性以及是否过期。

验证 JWT 的合法性需要使用**应用的密钥**，密钥在控制台中的设置面板中可以获取到，如下图所示：

![&#x82E5;&#x4F60;&#x7684;&#x5BC6;&#x94A5;&#x53D1;&#x751F;&#x6CC4;&#x6F0F;&#xFF0C;&#x8BF7;&#x70B9;&#x51FB;&#x300C;&#x5237;&#x65B0;&#x300D;&#x91CD;&#x7F6E;&#x5BC6;&#x94A5;](../../.gitbook/assets/image%20%2861%29.png)

以下验证合法性的代码以 Node 为例（需要安装 `jsonwebtoken`）。

```javascript
const jwt = require('jsonwebtoken');

try {
  let decoded = jwt.verify('JSON Web Token from client', 'your_secret'),
    expired = (Date.parse(new Date()) / 1000) > decoded.exp
  if (expired) {
    // 过期
  }else {
    // 合法也没过期，正常放行
  }
} catch (error) {
  // 不合法
}
```

  




