# 验证 JWT Token

JWT Token 是用户登录后的唯一凭证，验证 Token 有两种方法：

1. [发送 Token 给 Authing 服务器验证](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token#fa-song-token-gei-authing-fu-wu-qi-yan-zheng)
2. [使用 OIDC 应用的密钥（secret）验证 Token](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token#shi-yong-oauth-huo-oidc-ying-yong-de-mi-yao-yan-zheng-token)

## 发送 Token 给 Authing 服务器验证

{% hint style="info" %}
此方式适用于未[使用 OIDC](https://learn.authing.cn/authing/advanced/oidc) 流程的应用进行验证，若你使用了 OIDC，请参考：[使用 OIDC 应用的密钥验证 Token](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token#shi-yong-oauth-huo-oidc-ying-yong-de-mi-yao-yan-zheng-token)。
{% endhint %}

Authing 使用 [GraphQL](http://graphql.cn/) 进行数据交换，以下以 JavaScript 为例，演示如何发送 Token 给 Authing 服务器：

```javascript
import axios from 'axios'

axios({
  method: 'POST',
  operationName: 'checkLoginStatus',
  query: `query checkLoginStatus($token: String) {
    checkLoginStatus(token: $token) {
      status
      code
      message
      token {
        data {
          email
          id
          clientId
          unionid
        }
        iat
        exp
      }
    }
  }`,
  variables: {
    token: 'USER_JWT_TOKEN'
  },
}).then((res) => {
  const d = res.data;
  if (d.errors) {
    throw d.errors[0];
  }
  return d.data.checkLoginStatus;
});
.then((loginStatus) => {
  // handle login status
})
.catch((error) => {
  // handle error
});
```

若你使用了 Web SDK，使用上可以简单一些，如下所示：

```javascript
import Authing from 'authing-js-sdk'

(async () => {
    const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
    });
    
    
    const result = await authing.checkLoginStatus('USER_JWT_TOKEN');
})()
```

若 Token 合法，则返回数据为：

```javascript
{
  status: false,
  code: 200,
  message: '已登录',
  token: {
    ... // Token 数据
  }
}
```

当 status 为 false 时，有三种情况，分别返回：

```javascript
{
  status: false,
  code: 2020,
  message: '未登录'
}
```

```javascript
{
  status: false,
  code: 2206,
  message: '登录信息已过期' 
}
```

```javascript
{
     status: false,
     code: 2207,
     message: '登录信息有误'
 }
```

若你使用的为非 JavaScript 语言，请参考以下链接查看如何发送 GraphQL 请求：

### 各语言使用 GraphQL 的方法

{% embed url="http://graphql.cn/code/\#%E6%9C%8D%E5%8A%A1%E7%AB%AF%E5%BA%93" %}

### 验证 JWT Token 合法性的 GraphQL

```graphql
query checkLoginStatus($token: String) {
    checkLoginStatus(token: $token) {
      status
      code
      message
      token {
        data {
          email
          id
          clientId
        }
        iat
        exp
      }
    }
  }
```

## 使用 OIDC 应用的密钥验证 Token

{% hint style="info" %}
如果你使用了 OIDC 流程，请使用此方式验证 Token。
{% endhint %}

密钥在控制台中 OIDC 应用的详情中可以获取到，如下图所示：

![](../../.gitbook/assets/image%20%28173%29.png)

![](../../.gitbook/assets/image%20%286%29.png)

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

{% hint style="info" %}
为了避免在客户端暴露 Token，建议将 Token 验证放在服务端执行。
{% endhint %}

  




