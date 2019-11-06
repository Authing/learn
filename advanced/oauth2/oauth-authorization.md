# 使用 OAuth 授权

## 使用 authorization\_code 模式

以下流程图列举了一种使用 OAuth 协议授权码模式的方式。

![&#x6388;&#x6743;&#x7801;&#x6A21;&#x5F0F;&#x6D41;&#x7A0B;&#x56FE;](../../.gitbook/assets/image%20%28153%29.png)

### **1. 请求授权**

首先需要请求以下链接来获取 `authorization_code`

`https://sso.authing.cn/authorize?app_id=5c7253efe21948de32723725&state=123456lkjljkf3&response_type=code&redirect_uri=https%3A%2F%2Fauthing.cn&scope=user`

#### 参数说明

| 参数 | 说明 |
| :--- | :--- |
| app\_id | 必须，创建 OAuth 应用后详情中的 `App ID` |
| response\_type | 必须，授权的类型，在 `authorization_code 模式` 中值必须为 `"code"` |
| redirect\_uri | 必须，授权成功后的回掉地址，必须为 OAuth 应用配置好的 `URL` 中的一个 |
| state | 一段随机字符串，主要用于防止跨站请求伪造攻击，它将原封不动地在 `redirect_uri` 中返回 |
| scope | 允许的授权的范围，暂时没有实现此字段的相关功能，可不传 |

### **2. 重定向并获取 `access_token`**

`{redirect_uri}?code=8cce9189ee40f6f8874a9d4618a2996ece7dd737&state=123456lkjljkf3`

当授权成功后，用户将会被重定向到如上的链接，链接中带有参数 `code` 和 `state`。在 `redirect_uri` 的请求处理中，你应该检查 `state` 参数是否与你请求时一致，这样能确保请求不是来自第三方应用。然后，你应该使用 `POST` 请求`https://sso.authing.cn/token` 来获取 `access_token`，需要携带以下参数

| 参数 | 说明 |
| :--- | :--- |
| app\_id/client\_id | 必须，创建 OAuth 应用后详情中的 `App ID` |
| app\_secret/client\_secret | 必须，创建 OAuth 应用后详情中的 `App Secret` |
| code | 必须，我们返回的 `code` |
| redirect\_uri | 必须，授权成功后的回掉地址，必须为 OAuth 应用配置好的 `URL` 中的一个，需要经过 urlencoded 编码 |
| grant\_type | 必须，授权类型，在 `authorization_code 模式`中必须为 `"authorization_code"` |

同时 `Content-Type` 为 `application/x-www-form-urlencoded`。

例如：

```text
curl --request POST \
  --url https://sso.authing.cn/token \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'app_id=<APP_ID>&app_secret=<APP_SECRET>&grant_type=authorization_code&code=<CODE>&redirect_uri=<REDIRECT_URI>'    
```

返回：

```text
{
  "access_token":"4331396b953d3de3bcf74c564455323d0989a9a0",
  "token_type":"Bearer",
  "expires_in":3599, // token 有效时间
  "refresh_token":"017888c6722e3433f3840a6708d9b79c23b310b8" // 可用此 token 来重新获取新的 token 而无需重新授权
}
```

### **3. 使用 `access_token` 请求 `userInfo`**

`GET https://users.authing.cn/oauth/user/userinfo?access_token=...`

或作为 `Authoriztion` 头：

`Authorization: Bearer { access_token }`

### **4. 刷新 `access_token`**

`access_token` 的有效时间只有 1 小时，`refresh_token` 的有效时间为 2 周，所以在申请 `access_token` 一小时以后，可通过 `POST` 请求`https://sso.authing.cn/token` 来重新获取 `access_token`，需要携带以下参数：

| 参数 | 说明 |
| :--- | :--- |
| app\_id | 必须，创建 OAuth 应用后详情中的 `App ID` |
| app\_secret | 必须，创建 OAuth 应用后详情中的 `App Secret` |
| refresh\_token | 必须，之前获取 `access_token` 时返回的 `refresh_token` |
| grant\_type | 必须，授权类型，在刷新 token 时必须为 `"refresh_token"` |

返回的数据与第二步一样。

## implicit 模式

与 `authorization_code` 不同，`implicit 模式` 直接返回 `access_token`，少了验证 `authorization_code` 的步骤，且不能刷新 `access_token`。

下面 implicit 模式时序图中红框部分是与授权码模式不同的地方。

![implicit &#x6A21;&#x5F0F;](../../.gitbook/assets/image%20%28144%29.png)

### **1. 发起授权**

`https://sso.authing.cn/authorize?app_id=5c7253efe21948de32723725&state=123456lkjljkf3&response_type=token&redirect_uri=https%3A%2F%2Fauthing.cn&scope=user`

通过请求以上链接可直接获取 `access_token`。

参数说明

| 参数 | 说明 |
| :--- | :--- |
| app\_id | 必须，创建 OAuth 应用后详情中的 `App ID` |
| response\_type | 必须，授权的类型，在 `implicit 模式` 中值必须为 `"token"` |
| redirect\_uri | 必须，授权成功后的回掉地址，必须为 OAuth 应用配置好的 `URL` 中的一个 |
| scope | 允许的授权的范围，暂时没有实现此字段的相关功能，可不传 |

### **2. 重定向并返回 `access_token`**

`{redirect_uri}#access_token=9a617eb1dddc9fc7a480b0778173fd7f9db33938&state=123456lkjljkf3`

当授权成功后，将会重定向到如上的链接，在 `hash` 中携带 `access_token`。

### **3. 使用 `access_token` 请求 userInfo**

与 `authorization_code 模式` 的第三步一样。

`GET https://users.authing.cn/oauth/user/userinfo?access_token=...`

或作为 `Authoriztion` 头：

`Authorization: Bearer { access_token }`

## password 模式

此模式要求有用户名和密码，且必须使用 `POST` 请求

![password &#x6A21;&#x5F0F;](../../.gitbook/assets/image%20%2869%29.png)

### **1. 发起授权**

向以下这个地址发起 POST 请求：

`https://sso.authing.cn/token`

参数说明

| 参数 | 说明 |
| :--- | :--- |
| app\_id | 必须，创建 OAuth 应用后详情中的 `App ID` |
| app\_secret | 必须，创建 OAuth 应用后详情中的 `App Secret` |
| grant\_type | 必须，授权的类型，在 `password 模式` 中值必须为 `"password"` |
| username | 必须，用户邮箱 |
| password | 必须，用户密码 |

返回

```text
{
  "access_token":"4331396b953d3de3bcf74c564455323d0989a9a0",
  "token_type":"Bearer",
  "expires_in":3599, // token 有效时间
  "refresh_token":"017888c6722e3433f3840a6708d9b79c23b310b8" // 可用此 token 来重新获取新的 token 而无需重新授权
}
```

### **2. 使用 `access_token` 请求 userInfo**

与 `authorization_code 模式` 的第三步一样。

`GET https://users.authing.cn/oauth/user/userinfo?access_token=...`

或作为 `Authoriztion` 头：

`Authorization: Bearer { access_token }`

## client\_credentials 模式

此模式用于获取对应 client 的所有用户信息，请谨慎使用，不支持刷新 token

### **1. 发起授权**

向以下这个地址发起 POST 请求：

`https://sso.authing.cn/token`

参数说明

| 参数 | 说明 |
| :--- | :--- |
| app\_id | 必须，创建 OAuth 应用后详情中的 `App ID` |
| app\_secret | 必须，创建 OAuth 应用后详情中的 `App Secret` |
| grant\_type | 必须，授权的类型，在 `client_credentials 模式` 中值必须为 `"client_credentials"` |

返回

```text
{
  "access_token":"4331396b953d3de3bcf74c564455323d0989a9a0",
  "token_type":"Bearer",
  "expires_in":3599, // token 有效时间
}
```

### **2. 使用 `access_token` 请求 userInfo**

与 `authorization_code 模式` 的第三步一样。

`GET https://users.authing.cn/oauth/user/userinfo?access_token=...`

或作为 `Authoriztion` 头：

`Authorization: Bearer { access_token }`

## **常见问题**

### **验证 access\_token 的 URL 地址**

{% api-method method="get" host="https://sso.authing.cn/" path="authenticate?access\_token=" %}
{% api-method-summary %}
 验证 access\_token
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="access\_token" type="string" required=false %}
access\_token 的值
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
  "_id": "5d60efdee3faa629cccc0dee",
  "id": "5d60efdee3faa629cccc0dee",
  "accessToken": "db8abb9e16de36f52da082a6dc9f20c378301fc8",
  "accessTokenExpiresAt": "2019-08-24T09:05:50.201Z",
  "scope": "user",
  "grantType": "implicit",
  "appId": "5d60e64f62554964c85b555e",
  "userOrClientId": "5cbecd1daee8f920472cb40a",
  "when": "2019-08-24T08:05:50.201Z",
  "iss": "https://sso.authing.cn",
  "sub": "5d60efdee3faa629cccc0dee",
  "aud": "5d60e64f62554964c85b555e",
  "exp": 1566637550201,
  "iat": 1566633950201,
  "user_id": "5d60efdee3faa629cccc0dee",
  "issued_to": "https://sso.authing.cn",
  "audience": "5d60e64f62554964c85b555e",
  "expires_in": 3360,
  "access_type": "offline"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### **Token 有效时间**

> 所有模式的 `access_token` 有效时间都是 1 小时，能够使用 `refresh_token` 的模式中的 `refresh_token` 的有效时间为 2 周。

### **四种模式的区别**

> 关于这四种模式的应用场景和区别，建议浏览 [理解 OAuth2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)，一般情况下只需开启 `authorization_code` 模式。

