# 验证 JWT Token

JWT Token 是用户登录后的唯一凭证，验证 Token 有三种方法：

1. [发送 Token 给 Authing 服务器验证](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token#fa-song-token-gei-authing-fu-wu-qi-yan-zheng)
2. [使用 OIDC 应用的密钥（secret）验证 Token](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token#oidc-secret-token)
3. [通过 OIDC 应用或 OAuth 应用的在线验证接口验证](verify-jwt-token.md#zai-xian-yan-zheng)

## 发送 Token 给 Authing 服务器验证

{% hint style="info" %}
此方式适用于验证**扫码登录**或**直接调用登录接口**后获取到的 Token。
{% endhint %}

{% api-method method="get" host="https://users.authing.cn" path="/authing/token" %}
{% api-method-summary %}
 验证 Authing 签发的 Token
{% endapi-method-summary %}

{% api-method-description %}
 此方式适用于验证扫码登录或者直接调用登录接口后获取到的 Token。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="access\_token" type="string" required=true %}

{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
	"status": true,
	"message": "已登录",
	"code": 200,
	"token": {
		"data": {
			"email": "YOUR_EMAIL@domain.com",
			"id": "YOUR_USER_ID",
			"clientId": "YOUR_UESR_POOL_ID"
		},
		"iat": "Token 签发时间"
		"exp": "Token 过期时间"
	}
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### 请求示例

```javascript
$ curl https://users.authing.cn/authing/token?access_token=YOUR_TOKEN
```

### 返回结果示例

```javascript
{
	"status": true,
	"message": "已登录",
	"code": 200,
	"token": {
		"data": {
			"email": "YOUR_EMAIL@domain.com",
			"id": "YOUR_USER_ID",
			"clientId": "YOUR_UESR_POOL_ID"
		},
		"iat": "Token 签发时间"
		"exp": "Token 过期时间"
	}
}
```

{% hint style="info" %}
字段解释：

1. status：Token 状态，true 为正常，false 为非法；
2. code：状态代码，200 正常，非 200 不正常；
3. message：提示信息；
4. token：
   1. data：用户信息，email 为用户邮箱，id 为用户的唯一标识，clientId 为用户池 id，unioind 为用户使用三方登录时用户在三方登录平台的 id；
   2. iat：Token 签发时间；
   3. exp：Token 过期时间；
{% endhint %}

## 使用 OIDC 应用的密钥验证 Token <a id="oidc-secret-token"></a>

{% hint style="info" %}
如果你使用了 OIDC 流程，请使用此方式验证 Token。
{% endhint %}

密钥在控制台中 OIDC 应用的详情中可以获取到，如下图所示：

![](../.gitbook/assets/image%20%28295%29.png)

![](../.gitbook/assets/image%20%2811%29.png)

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

如果你对如何在后端处理 OIDC 有困惑，请参考 Github 上的示例代码：[oidc-demo](https://github.com/Authing/oidc-demo)。

## 在线验证 OIDC 或 OAuth Token

{% api-method method="get" host="https://oauth.authing.cn" path="/oauth/oidc/validate\_access\_token" %}
{% api-method-summary %}
验证 OIDC access\_token 或 id\_token 的合法性
{% endapi-method-summary %}

{% api-method-description %}
验证 OIDC 相关 token 合法性的线上接口。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="access\_token" type="string" required=true %}
值为 access\_token 或 id\_token
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    "state": 1,
    "isRevoked": false,
    "isDeleted": false,
    "_id": "yokj1gN8kCBixIhc6KEj7SNsMcJ",
    "id": "yokj1gN8kCBixIhc6KEj7SNsMcJ",
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJkVlJmenZEbHZQRG50dG9LWnJ1WkciLCJzdWIiOiI1Y2U1M2FlYTlmODUyNTdkZDEzMmQ3NDkiLCJpc3MiOiJodHRwczovL29hdXRoLmF1dGhpbmcuY24vb2F1dGgvb2lkYyIsImlhdCI6MTU2OTU4MDMwOCwiZXhwIjoxNTY5NTgzOTA1LCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIiwiYXVkIjoiNWQwMWUzODk5ODVmODFjNmMxZGQzMWRlIn0.RZ1NWMajncZggkpBt7iWxhHG3QhP8nIqWKaGysyujYo",
    "accessTokenExpiresAt": "2019-09-27T11:31:45.000Z",
    "scope": "openid profile",
    "appId": "5d01e389985f81c6c1dd31de",
    "userOrClientId": "5ce53aea9f85257dd132d749",
    "when": "2019-09-27T10:31:48.000Z",
    "iss": "https://oauth.authing.cn/oauth/oidc",
    "sub": "5ce53aea9f85257dd132d749",
    "aud": "5d01e389985f81c6c1dd31de",
    "exp": 1569583905000,
    "iat": 1569580308000,
    "user_id": "5ce53aea9f85257dd132d749",
    "issued_to": "https://sso.authing.cn",
    "audience": "5d01e389985f81c6c1dd31de",
    "expires_in": 3360,
    "access_type": "offline"
}
{
    "code": 1920,
    "message": "查找 session 发生错误"
}
{
    "code": 1921,
    "message": "session 不存在"
}
{
    "code": 1922,
    message: "token 不合法"
}
{
    "code": 1923,
    "message": "token 过期"
}
{
    "code": 1924,
    "message": "app 不存在"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### 请求示例

```javascript
$ curl https://oauth.authing.cn/oauth/oidc/validate_access_token?access_token=YOUR_TOKEN
```

{% api-method method="get" host="https://oauth.authing.cn" path="/authenticate" %}
{% api-method-summary %}
验证 OAuth access\_token 合法性
{% endapi-method-summary %}

{% api-method-description %}
验证 OAuth token 合法性的线上接口
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="access\_token" type="string" required=true %}
OAuth 签发的 access\_token
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    "state": 1,
    "token": {
        "isRevoked": false,
        "isDeleted": false,
        "_id": "5d8df3d12914983cab6430b1",
        "accessToken": "b4177a2e01060169fc724112b10703fda49e7d69",
        "accessTokenExpiresAt": "2019-09-27T12:34:41.480Z",
        "refreshToken": "fd56d928ed3f00b18db14550d63d9f3a051812fc",
        "refreshTokenExpiresAt": "2019-10-11T11:34:41.480Z",
        "scope": "profile",
        "grantType": "authorization_code",
        "appId": "5ce52da3b0148671e7f5bfd7",
        "userOrClientId": "5ce53aea9f85257dd132d749",
        "when": "2019-09-27T11:34:41.481Z",
        "__v": 0
    },
    "isRevoked": false,
    "isDeleted": false,
    "_id": "5d8df3d12914983cab6430b1",
    "id": "5ce53aea9f85257dd132d749",
    "accessToken": "b4177a2e01060169fc724112b10703fda49e7d69",
    "accessTokenExpiresAt": "2019-09-27T12:34:41.480Z",
    "scope": "profile",
    "grantType": "authorization_code",
    "appId": "5ce52da3b0148671e7f5bfd7",
    "userOrClientId": "5ce53aea9f85257dd132d749",
    "when": "2019-09-27T11:34:41.481Z",
    "iss": "https://sso.authing.cn",
    "sub": "5ce53aea9f85257dd132d749",
    "aud": "5ce52da3b0148671e7f5bfd7",
    "exp": 1569587681480,
    "iat": 1569584081481,
    "user_id": "5ce53aea9f85257dd132d749",
    "issued_to": "https://sso.authing.cn",
    "audience": "5ce52da3b0148671e7f5bfd7",
    "expires_in": 3360,
    "access_type": "offline"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### 请求示例

```javascript
$ curl https://oauth.authing.cn/authenticate?access_token=YOUR_TOKEN
```

