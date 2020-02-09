---
description: 结合 Authing 实现 OIDC 授权的方法。
---

# 使用 OIDC 授权

## 术语

1. `End-User`：终端用户，也可以理解为使用你软件的人
2. `RP（Relying-Party）`：服务器后端
3. `AP（Authentication Provider）`： Authing 服务器

P.S. 文档中出现的 `testapp.authing.cn` 和 `example.authing.cn` 两个域名是可以在控制台配置的二级域名。

## 在 Authing 中创建一个应用

使用 OIDC 需要先[注册一个 Authing 账号](https://authing.cn/login)并[新建一个应用](create-oidc.md#chuang-jian-oidc-ying-yong)。

## 创建 OIDC 应用

在完成了 Authing 应用的创建后相当于你拥有了一个用户池，接下来需要创建一个 OIDC 应用，这样你可以在其他第三方软件中读取用户池中的用户数据，其你给参考：

{% page-ref page="create-oidc.md" %}

## OIDC 的基本流程

1. 用户执行登录
2. 登录成功后回调到开发者配置好的 redirect\_uri 中并附带参数
3. 如果返回类型是 code，那么开发者需要在后端使用 code 和 secret（用户创建完 OIDC 应用后会得到）换取 access\_token
4. 如果返回类型是 id\_token token，那么在用户登录成功后的回调 URI 中会直接附带 id\_token 和 access\_token
5. 使用 access\_token 可以换取用户信息（userInfo）

如果你想直观的体验 OIDC 认证流程，请[点击这里查看我们提供的示例](https://first-oidc-app.authing.cn)或[点击这里可视化的理解 OIDC](https://openidconnect.net/)。

如果你对如何在后端处理 OIDC 有困惑，请参考 Github 上的示例代码：[oidc-demo](https://github.com/Authing/oidc-demo)。

## 使用授权码模式（Authorization Code Flow）

这个小节介绍如何使用 code （response\_type 为 code）换取 access\_token（access\_token 可用来换取用户信息）。

### 01 - 在控制台配置 OIDC 应用

打开 `authorization_code` 模式，并选择 code 返回类型。

![](../../.gitbook/assets/image%20%28215%29.png)

### 02 - 发起登录请求

{% api-method method="get" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/auth" %}
{% api-method-summary %}
 发起 OIDC 登录请求
{% endapi-method-summary %}

{% api-method-description %}
发起授权需要拼接一个用来授权的 URL，具体参数如下：
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="client\_id" type="string" required=true %}
OIDC 应用的 app\_id
{% endapi-method-parameter %}

{% api-method-parameter name="redirect\_uri" type="string" required=true %}
在控制台配置的 OIDC 回调 url 其中的一个值
{% endapi-method-parameter %}

{% api-method-parameter name="scope" type="string" required=true %}
需要请求的权限，如果需要获取 unionid 需要包含 unionid，如果需要获取手机号和 email 需要有 phone email，如果需要 refresh\_token 需要包含 offline\_access。同时 id\_token 中会包含相关的字段。
{% endapi-method-parameter %}

{% api-method-parameter name="response\_type" type="string" required=true %}
OIDC 模式，可以为 code, id\_token, id\_token token, code id\_token, code token, code id\_token token
{% endapi-method-parameter %}

{% api-method-parameter name="prompt" type="string" required=true %}
可以为 none，login，consent 或 select\_account，指定 AP 与 End-User 的交互方式，如需 refresh\_token，必须为 consent
{% endapi-method-parameter %}

{% api-method-parameter name="state" type="string" required=true %}
一个随机字符串，用于防范 CSRF 攻击，如果 response 中的 state 值和发送请求之前设置的 state 值不同，说明受到攻击
{% endapi-method-parameter %}

{% api-method-parameter name="nonce" type="string" required=true %}
 一个随机字符串，用于防范 Replay 攻击
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

请求示例：

```text
https://<你的应用域名>.authing.cn/oauth/oidc/auth?client_id=5c9b079883e333d55a101082&redirect_uri=https://www.example.cn/example&scope=openid profile&response_type=code&state=jacket
```

**如需后续刷新 access\_token**，请按照以下方式拼接登录链接

带刷新 token 功能的登录请求示例：

```text
https://<你的应用域名>.authing.cn/oauth/oidc/auth?client_id=5c9b079883e333d55a101082&redirect_uri=https://example.com&scope=openid profile offline_access&response_type=code&prompt=consent&state=235345
```

参考资料：

1. [scope 与用户信息对应表](https://docs.authing.cn/authing/advanced/oidc/oidc-params#scope-can-shu-dui-ying-de-yong-hu-xin-xi)
2. [response\_type 参数 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)
3. [prompt 参数 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)

### 03 - 用户登录

上一个请求验证通过后会重定向到 Authing 提供的登录框页面，此时用户需要输入他的用户名和密码进行登录。

> 你可以前往这个网址体验：https://sample-sso.authing.cn/login

![](../../.gitbook/assets/image%20%2852%29.png)



此时 Authing 会验证此用户是否合法，如果合法则会跳到用户配置好的 redirect\_uri 中并附带 code 参数。

### 04 - 使用 code 换取 token

{% api-method method="post" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/token" %}
{% api-method-summary %}
client\_secret\_post 方式换取 token
{% endapi-method-summary %}

{% api-method-description %}
如果你在控制台配置 OIDC 时，换取 token 方式设置的为 client\_secret\_post，那么按照下面这种方法换取 token。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Content-Type" type="string" required=true %}
application/x-www-form-urlencoded
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-form-data-parameters %}
{% api-method-parameter name="client\_id" type="string" required=true %}
OIDC 应用的 App Secret
{% endapi-method-parameter %}

{% api-method-parameter name="client\_secret" type="string" required=true %}
OIDC 应用的 App Secret
{% endapi-method-parameter %}

{% api-method-parameter name="grant\_type" type="string" required=true %}
authorization\_code
{% endapi-method-parameter %}

{% api-method-parameter name="redirect\_uri" type="string" required=true %}
在控制台配置的 OIDC 回调 url 中其中一个值
{% endapi-method-parameter %}

{% api-method-parameter name="code" type="string" required=true %}
获取到的授权码
{% endapi-method-parameter %}
{% endapi-method-form-data-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJqdGkiOiJ4R01uczd5cmNFckxiakNRVW9US1MiLCJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJpc3MiOiJodHRwczovL2F1dGhpbmcuY24iLCJpYXQiOjE1NTQ1Mzc4NjksImV4cCI6MTU1NDU0MTQ2OSwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBvZmZsaW5lX2FjY2VzcyBwaG9uZSBlbWFpbCIsImF1ZCI6IjVjYTc2NWUzOTMxOTRkNTg5MWRiMTkyNyJ9.wX05OAgYuXeYM7zCxhrkvTO_taqxrCTG_L2ImDmQjMml6E3GXjYA9EFK0NfWquUI2mdSMAqohX-ndffN0fa5cChdcMJEm3XS9tt6-_zzhoOojK-q9MHF7huZg4O1587xhSofxs-KS7BeYxEHKn_10tAkjEIo9QtYUE7zD7JXwGUsvfMMjOqEVW6KuY3ZOmIq_ncKlB4jvbdrduxy1pbky_kvzHWlE9El_N5qveQXyuvNZVMSIEpw8_y5iSxPxKfrVwGY7hBaF40Oph-d2PO7AzKvxEVMamzLvMGBMaRAP_WttBPAUSqTU5uMXwMafryhGdIcQVsDPcGNgMX6E1jzLA",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJub25jZSI6IjIyMTIxIiwiYXRfaGFzaCI6Ik5kbW9iZVBZOEFFaWQ2T216MzIyOXciLCJzaWQiOiI1ODM2NzllNC1lYWM5LTRjNDEtOGQxMS1jZWFkMmE5OWQzZWIiLCJhdWQiOiI1Y2E3NjVlMzkzMTk0ZDU4OTFkYjE5MjciLCJleHAiOjE1NTQ1NDE0NjksImlhdCI6MTU1NDUzNzg2OSwiaXNzIjoiaHR0cHM6Ly9hdXRoaW5nLmNuIn0.IQi5FRHO756e_eAmdAs3OnFMU7QuP-XtrbwCZC1gJntevYJTltEg1CLkG7eVhdi_g5MJV1c0pNZ_xHmwS0R-E4lAXcc1QveYKptnMroKpBWs5mXwoOiqbrjKEmLMaPgRzCOdLiSdoZuQNw_z-gVhFiMNxI055TyFJdXTNtExt1O3KmwqanPNUi6XyW43bUl29v_kAvKgiOB28f3I0fB4EsiZjxp1uxHQBaDeBMSPaRVWQJcIjAJ9JLgkaDt1j7HZ2a1daWZ4HPzifDuDfi6_Ob1ZL40tWEC7xdxHlCEWJ4pUIsDjvScdQsez9aV_xMwumw3X4tgUIxFOCNVEvr73Fg",
  "refresh_token": "WPsGJbvpBjqXz6IJIr1UHKyrdVF",
  "scope": "openid profile offline_access phone email",
  "token_type": "Bearer"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}



这里有[完整的 nodejs 示例代码](https://github.com/Authing/oidc-demo)：

```javascript
let code2tokenResponse
try {
  code2tokenResponse = await axios.post(
    "https://oauth.authing.cn/oauth/oidc/token",
    qs.stringify({
      code,
      client_id: oidcAppId,
      client_secret: oidcAppSecret,
      grant_type: "authorization_code",
      redirect_uri
    }),
    {
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded'
      }
    }
  );
} catch (error) {
  ctx.body = error.response.data
  return
}
```

返回示例：

```text
{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IjEifQ.eyJqdGkiOiJ-azVqal9LelNoSl9adGg0RFc1YmkiLCJzdWIiOiI1Y2U1M2FlYTlmODUyNTdkZDEzMmQ3NDkiLCJpc3MiOiJodHRwczovL29hdXRoLmF1dGhpbmcuY24vb2F1dGgvb2lkYyIsImlhdCI6MTU4MDcxOTU5NSwiZXhwIjoxNTgwNzIzMTkyLCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIGVtYWlsIHBob25lIGFkZHJlc3Mgb2ZmbGluZV9hY2Nlc3MiLCJhdWQiOiI1ZDAxZTM4OTk4NWY4MWM2YzFkZDMxZGUifQ.OBcLmACMYnXnB6OYcjwC7K5GpP-TOoQhtXtNrqVZfsbmHYU3P4uO68nI41wlcwYhH_J9V3XphmuVK-RT9RiuZN0a7BmgY1YJVF1piF28u4KLoOnz8sMo2ccZAaMxiFBiqInYPn02rhQK9Qbxp7oZcwlmtV74k_ih-GS__cDHDWGbNB-dzT75fsc3aXpBTJv3yutJeThRtHGNZn50R196VkcEUE9oGbdPbKLzzi_dM0bQ5Lq3BlXrlzuj4Nc6iOoZvJhh-eogtoQ7jjtGH42ESmTsGbob4uvDZXS04FttE8uQ4T-VD1h_BB651ZLPa4nQGsWfRr9d0ALXTCLBvyvuiQ",
    "expires_in": 3597,
    "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IjEifQ.eyJzdWIiOiI1Y2U1M2FlYTlmODUyNTdkZDEzMmQ3NDkiLCJiaXJ0aGRhdGUiOiIiLCJmYW1pbHlfbmFtZSI6IiIsImdlbmRlciI6IiIsImdpdmVuX25hbWUiOiIiLCJsb2NhbGUiOiIiLCJtaWRkbGVfbmFtZSI6IiIsIm5hbWUiOiIiLCJuaWNrbmFtZSI6IiIsInBpY3R1cmUiOiJodHRwczovL3VzZXJjb250ZW50cy5hdXRoaW5nLmNuL2F1dGhpbmctYXZhdGFyLnBuZyIsInByZWZlcnJlZF91c2VybmFtZSI6IiIsInByb2ZpbGUiOiIiLCJ1cGRhdGVkX2F0IjoiIiwid2Vic2l0ZSI6IiIsInpvbmVpbmZvIjoiIiwiZW1haWwiOiJ0ZXN0M0AxMjMuY29tIiwiZW1haWxfdmVyaWZpZWQiOmZhbHNlLCJwaG9uZV9udW1iZXJfdmVyaWZpZWQiOiIxMzExMjM0MTIzNCIsImFkZHJlc3MiOiIiLCJhdF9oYXNoIjoiMU9RLWRoTjZLakdEaDlHakNnUGNTdyIsInNpZCI6IjZmMmRhZmVmLTI0ZTQtNDBmNC04OTVhLTQwNjdhOTRmNDA2NyIsImF1ZCI6IjVkMDFlMzg5OTg1ZjgxYzZjMWRkMzFkZSIsImV4cCI6MTU4MDcyMzE5MiwiaWF0IjoxNTgwNzE5NTk1LCJpc3MiOiJodHRwczovL29hdXRoLmF1dGhpbmcuY24vb2F1dGgvb2lkYyJ9.vQNYBzZhcYUISonwhZE1FFpoqtxGR_aPbUwfvB-aQAFWCOimu0fPtJbTLLzKeAg8xptDZLIt4F2z9NFg3E33eTaaVhVKQSrpxqH0L1T1OK6T0EBZLQn2l8GFhG33AxCSI1uCZM6ncjSDAJ6zFXHsWXmBoZtfDhY29xc-HY520xV8AseFKxLLrI_y9lmqTI-9EYPCKDYi6l-G8FA9BFbDU04TQVHFYJ40LfZB18zBTkgNSkGdvutUjulFI_bp5_SKMtCH0WEAZPbV3fjuPisemgu7awc4pDOA_IrvnSpx6ktrUO6TBdFOgWIYNs8hSL65y666B_xkVc05gEpyCE70_g",
    "refresh_token": "kKXMlK4IybivfIJ25pmM5CjisyL",
    "scope": "openid profile email phone address offline_access",
    "token_type": "Bearer"
}
```

**id\_token** 中会**包含 scope 参数请求的信息**，例如邮箱、手机号，解析后的 id\_token：

```text
{
  "sub": "5ce53aea9f85257dd132d749",
  "birthdate": "",
  "family_name": "",
  "gender": "",
  "given_name": "",
  "locale": "",
  "middle_name": "",
  "name": "",
  "nickname": "",
  "picture": "https://usercontents.authing.cn/authing-avatar.png",
  "preferred_username": "",
  "profile": "",
  "updated_at": "",
  "website": "",
  "zoneinfo": "",
  "email": "test3@123.com",
  "email_verified": false,
  "phone_number": "13112341234",
  "address": "",
  "at_hash": "Djult1dxcKyR2DwssGFYkg",
  "sid": "6f2dafef-24e4-40f4-895a-4067a94f4067",
  "aud": "5d01e389985f81c6c1dd31de",
  "exp": 1580726037,
  "iat": 1580722440,
  "iss": "https://oauth.authing.cn/oauth/oidc"
}
```

{% api-method method="post" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/token" %}
{% api-method-summary %}
client\_secret\_basic 方式换取 token
{% endapi-method-summary %}

{% api-method-description %}
如果你在控制台配置 OIDC 时，换取 token 方式设置的为 client\_secret\_basic，那么按照下面这种方法换取 token。（client\_secret\_basic 是使用 HTTP Basic authentication 模式进行认证。）
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
Basic NWNhNzY1ZTM5MzE5NGQ1ODxxxx
{% endapi-method-parameter %}

{% api-method-parameter name="Content-Type" type="string" required=true %}
application/x-www-form-urlencoded
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-form-data-parameters %}
{% api-method-parameter name="grant\_type" type="string" required=true %}
 authorization\_code
{% endapi-method-parameter %}

{% api-method-parameter name="redirect\_uri" type="string" required=true %}
在控制台配置的 OIDC 回调 url 中其中一个值
{% endapi-method-parameter %}

{% api-method-parameter name="code" type="string" required=true %}
获取到的授权码
{% endapi-method-parameter %}
{% endapi-method-form-data-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJqdGkiOiJ4R01uczd5cmNFckxiakNRVW9US1MiLCJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJpc3MiOiJodHRwczovL2F1dGhpbmcuY24iLCJpYXQiOjE1NTQ1Mzc4NjksImV4cCI6MTU1NDU0MTQ2OSwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBvZmZsaW5lX2FjY2VzcyBwaG9uZSBlbWFpbCIsImF1ZCI6IjVjYTc2NWUzOTMxOTRkNTg5MWRiMTkyNyJ9.wX05OAgYuXeYM7zCxhrkvTO_taqxrCTG_L2ImDmQjMml6E3GXjYA9EFK0NfWquUI2mdSMAqohX-ndffN0fa5cChdcMJEm3XS9tt6-_zzhoOojK-q9MHF7huZg4O1587xhSofxs-KS7BeYxEHKn_10tAkjEIo9QtYUE7zD7JXwGUsvfMMjOqEVW6KuY3ZOmIq_ncKlB4jvbdrduxy1pbky_kvzHWlE9El_N5qveQXyuvNZVMSIEpw8_y5iSxPxKfrVwGY7hBaF40Oph-d2PO7AzKvxEVMamzLvMGBMaRAP_WttBPAUSqTU5uMXwMafryhGdIcQVsDPcGNgMX6E1jzLA",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJub25jZSI6IjIyMTIxIiwiYXRfaGFzaCI6Ik5kbW9iZVBZOEFFaWQ2T216MzIyOXciLCJzaWQiOiI1ODM2NzllNC1lYWM5LTRjNDEtOGQxMS1jZWFkMmE5OWQzZWIiLCJhdWQiOiI1Y2E3NjVlMzkzMTk0ZDU4OTFkYjE5MjciLCJleHAiOjE1NTQ1NDE0NjksImlhdCI6MTU1NDUzNzg2OSwiaXNzIjoiaHR0cHM6Ly9hdXRoaW5nLmNuIn0.IQi5FRHO756e_eAmdAs3OnFMU7QuP-XtrbwCZC1gJntevYJTltEg1CLkG7eVhdi_g5MJV1c0pNZ_xHmwS0R-E4lAXcc1QveYKptnMroKpBWs5mXwoOiqbrjKEmLMaPgRzCOdLiSdoZuQNw_z-gVhFiMNxI055TyFJdXTNtExt1O3KmwqanPNUi6XyW43bUl29v_kAvKgiOB28f3I0fB4EsiZjxp1uxHQBaDeBMSPaRVWQJcIjAJ9JLgkaDt1j7HZ2a1daWZ4HPzifDuDfi6_Ob1ZL40tWEC7xdxHlCEWJ4pUIsDjvScdQsez9aV_xMwumw3X4tgUIxFOCNVEvr73Fg",
  "refresh_token": "WPsGJbvpBjqXz6IJIr1UHKyrdVF",
  "scope": "openid profile offline_access phone email",
  "token_type": "Bearer"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

其中 `Authorization` 请求头 `Basic<空格>` 后的值为 `<client_id>:<client_secret>` 的 base64 值。

{% api-method method="post" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/token" %}
{% api-method-summary %}
none 方式换取 token
{% endapi-method-summary %}

{% api-method-description %}
如果你在控制台配置 OIDC 时，换取 token 方式设置的为 none，那么换取 token 时无需传递 client\_secret，其他参数和上表一样。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Content-Type" type="string" required=true %}
application/x-www-form-urlencoded
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-form-data-parameters %}
{% api-method-parameter name="client\_id" type="string" required=true %}
OIDC 应用的 App Secret
{% endapi-method-parameter %}

{% api-method-parameter name="grant\_type" type="string" required=true %}
authorization\_code
{% endapi-method-parameter %}

{% api-method-parameter name="redirect\_uri" type="string" required=true %}
在控制台配置的 OIDC 回调 url 中其中一个值
{% endapi-method-parameter %}

{% api-method-parameter name="code" type="string" required=true %}
获取到的授权码
{% endapi-method-parameter %}
{% endapi-method-form-data-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJqdGkiOiJ4R01uczd5cmNFckxiakNRVW9US1MiLCJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJpc3MiOiJodHRwczovL2F1dGhpbmcuY24iLCJpYXQiOjE1NTQ1Mzc4NjksImV4cCI6MTU1NDU0MTQ2OSwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBvZmZsaW5lX2FjY2VzcyBwaG9uZSBlbWFpbCIsImF1ZCI6IjVjYTc2NWUzOTMxOTRkNTg5MWRiMTkyNyJ9.wX05OAgYuXeYM7zCxhrkvTO_taqxrCTG_L2ImDmQjMml6E3GXjYA9EFK0NfWquUI2mdSMAqohX-ndffN0fa5cChdcMJEm3XS9tt6-_zzhoOojK-q9MHF7huZg4O1587xhSofxs-KS7BeYxEHKn_10tAkjEIo9QtYUE7zD7JXwGUsvfMMjOqEVW6KuY3ZOmIq_ncKlB4jvbdrduxy1pbky_kvzHWlE9El_N5qveQXyuvNZVMSIEpw8_y5iSxPxKfrVwGY7hBaF40Oph-d2PO7AzKvxEVMamzLvMGBMaRAP_WttBPAUSqTU5uMXwMafryhGdIcQVsDPcGNgMX6E1jzLA",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJub25jZSI6IjIyMTIxIiwiYXRfaGFzaCI6Ik5kbW9iZVBZOEFFaWQ2T216MzIyOXciLCJzaWQiOiI1ODM2NzllNC1lYWM5LTRjNDEtOGQxMS1jZWFkMmE5OWQzZWIiLCJhdWQiOiI1Y2E3NjVlMzkzMTk0ZDU4OTFkYjE5MjciLCJleHAiOjE1NTQ1NDE0NjksImlhdCI6MTU1NDUzNzg2OSwiaXNzIjoiaHR0cHM6Ly9hdXRoaW5nLmNuIn0.IQi5FRHO756e_eAmdAs3OnFMU7QuP-XtrbwCZC1gJntevYJTltEg1CLkG7eVhdi_g5MJV1c0pNZ_xHmwS0R-E4lAXcc1QveYKptnMroKpBWs5mXwoOiqbrjKEmLMaPgRzCOdLiSdoZuQNw_z-gVhFiMNxI055TyFJdXTNtExt1O3KmwqanPNUi6XyW43bUl29v_kAvKgiOB28f3I0fB4EsiZjxp1uxHQBaDeBMSPaRVWQJcIjAJ9JLgkaDt1j7HZ2a1daWZ4HPzifDuDfi6_Ob1ZL40tWEC7xdxHlCEWJ4pUIsDjvScdQsez9aV_xMwumw3X4tgUIxFOCNVEvr73Fg",
  "refresh_token": "WPsGJbvpBjqXz6IJIr1UHKyrdVF",
  "scope": "openid profile offline_access phone email",
  "token_type": "Bearer"
}

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

如果你想了解其他换取 Token 的方式，请[参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)。

### 05 - 验证 access\_token 和 id\_token 的合法性

#### 使用 OIDC 应用的 App Secret 检验

OIDC 默认使用 OIDC 应用的 secret 对 token 进行验证（也就是在创建应用时默认选择 `HS256` 算法）。

如果你使用 `javascript` 那么可以使用 jsonwebtoken 进行验证：

```javascript
const jwt = require('jsonwebtoken');
let decoded = jwt.verify(token, <appSecret>);
```

如果是其他语言，那么你在服务端需要用 app\_secret 作为 HS256 签名参数来计算签名和 JWT 中的签名进行对比，伪代码如下：

```text
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  "1133fd20c14e4cc29b6ecb71fb8eb952"// app_secret
)
```

如果是 RS256 等非对称加密算法，需要使用公钥验证签名。Authing 将使用私钥进行签名，请使用 Authing 的公钥来验证签名：

```text
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxRijj2seoesv5K0Z+ymR
K7DSDPxdsM2sGQD2ZVhLjLsxZWJtXUXh7ERdUU6OT3BqYZZf7CLIhN6yyNtTOgfg
pLG9HVJd7ZSKzuy2dS7mo8jD8YRtptAJmNFqw6z8tQp5MNG1ZHqp9isKqJmx/CFY
kRdXBmjjj8PMVSP757pkC3jCq7fsi0drSSg4lIxrSsGzL0++Ra9Du71Qe/ODQKU0
brxaI1OKILtfcVPTHTaheV+0dw4eYkSDtyaLBG3jqsQbdncNg8PCEWchNzdO6aaj
Uq4wbOzy/Ctp399mz0SGKfuC5S8gqAFABFT3DH3UD21ZztQZwFEV2AlvF+bcGEst
cwIDAQAB
-----END PUBLIC KEY-----
```

将 token 或 id\_token 发送到 Authing 提供的 token 验证接口进行验证

{% api-method method="get" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/validate\_access\_token" %}
{% api-method-summary %}
 在线验证 access\_token / id\_token 合法性
{% endapi-method-summary %}

{% api-method-description %}
Authing 提供了接口用于直接在线验证 access\_token 或 id\_token 的合法性。
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
 验证 access\_token 或 id\_token 时会有以下几种返回结果
{% endapi-method-response-example-description %}

```
access_token 或 id_token 合法时的返回结果
{
    "state": 1,
    "isRevoked": false,
    "isDeleted": false,
    "_id": "L3qRMJSE5F0tWlon1OnFhywimkp",
    "id": "L3qRMJSE5F0tWlon1OnFhywimkp",
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1Y2MzMmIyOGQ2ZWJhZTJmMjQwYTliZmQiLCJhdF9oYXNoIjoieWxHa2Y3a1pKZEg2VjRYbUNRMHFGQSIsInNpZCI6IjlhNzk1MDQ0LTM5MTUtNGI3ZS1hZThhLTAwZDc2YmVhMGIwZCIsImF1ZCI6IjVkMDIzYzdiNTY0MjQ2MTIzNGI5YzYyZSIsImV4cCI6MTU2Njg2NjE3NiwiaWF0IjoxNTY2ODYyNTc1LCJpc3MiOiJodHRwczovL29hdXRoLmF1dGhpbmcuY24vb2F1dGgvb2lkYyJ9.ZeaM9_iyNwwedNktJFdlFXyIGGE692MVpW4wbe8xR7A",
    "accessTokenExpiresAt": "2019-08-27T00:36:16.000Z",
    "appId": "5d023c7b5642461234b9c62e",
    "userOrClientId": "5cc32b28d6ebae2f240a9bfd",
    "when": "2019-08-26T23:36:15.000Z",
    "iss": "https://oauth.authing.cn/oauth/oidc",
    "sub": "5cc32b28d6ebae2f240a9bfd",
    "aud": "5d023c7b5642461234b9c62e",
    "exp": 1566866176000,
    "iat": 1566862575000,
    "user_id": "5cc32b28d6ebae2f240a9bfd",
    "issued_to": "https://sso.authing.cn",
    "audience": "5d023c7b5642461234b9c62e",
    "expires_in": 3360,
    "access_type": "offline"
}

token 签名错误
{
    code: 1922,
    message: "token 不合法"
}

token 过期
{
    code: 1923,
    message: "token 过期"
}

发生其他错误
{
    code: 1921,
    message: "session 不存在"
}
{
    code: 1920,
    message: "查找 session 发生错误"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

**参考链接**

1. jwks [参考规范](https://openid.net/specs/openid-connect-registration-1_0.html#ClientMetadata)；
2. 可以检验 jwt 的签名的 playground：[https://jwt.io](https://jwt.io)；
3. RSA 的 pem 格式 与 jwk 格式互转：[https://8gwifi.org/jwkconvertfunctions.jsp](https://8gwifi.org/jwkconvertfunctions.jsp)；
4. 生成 jwk：[https://mkjwk.org/](https://mkjwk.org/)；

### 06 - 使用 access\_token 换取用户信息

开发者在自己的服务中可以使用 access\_token 换取用户信息。根据 scope 的不同，这里的返回信息也会有所不同，字段符合 [OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationExamples)，字段解释请参考[用户信息字段含义](https://docs.authing.cn/authing/advanced/oidc/oidc-params#yong-hu-xin-xi-zi-duan-han-yi)。

{% api-method method="get" host="https://users.authing.cn" path="/oauth/oidc/user/userinfo" %}
{% api-method-summary %}
 使用 access\_token 换取用户信息
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="access\_token" type="string" required=true %}
 access\_token
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "sub": "<用户在 Authing 的唯一标识>",
  "nickname": "Authing",
  "name": "张三",
  "locale": "en-US"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

请求链接示例：[https://users.authing.cn/oauth/oidc/user/userinfo?access\_token=&lt;access\_token&gt;](https://users.authing.cn/oauth/oidc/user/userinfo?access_token=<access_token>
)

更多字段解释请参考[用户信息字段含义](https://docs.authing.cn/authing/advanced/oidc/oidc-params#yong-hu-xin-xi-zi-duan-han-yi)。

### 07 - 刷新 token

{% api-method method="post" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/token" %}
{% api-method-summary %}
   07-  刷新 token
{% endapi-method-summary %}

{% api-method-description %}
同时发起登录请求时必须填写正确的参数；  
如需使用刷新 token 功能，需要在控制台 OIDC 应用设置中勾选 refresh\_ token。  
同时发起登录请求时必须填写正确的参数，带刷新 token 功能的登录请求示例：  
`https://example.authing.cn/oauth/oidc/auth?client_id=5c9b079883e333d55a101082&redirect_uri=https://example.com&scope=openid profile offline_access&response_type=code&prompt=consent&state=235345`  
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Content-Type" type="string" required=true %}
 application/x-www-form-urlencoded
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-form-data-parameters %}
{% api-method-parameter name="client\_id" type="string" required=true %}
OIDC 应用的 App Id
{% endapi-method-parameter %}

{% api-method-parameter name="client\_secret" type="string" required=true %}
OIDC 应用的 App Secret
{% endapi-method-parameter %}

{% api-method-parameter name="grant\_type" type="string" required=true %}
refresh\_token
{% endapi-method-parameter %}

{% api-method-parameter name="refresh\_token" type="string" required=true %}
code 换 token 接口返回的 refresh\_token。例：WPsGJbvpBjqXz6IJIr1UHKyrdVF
{% endapi-method-parameter %}
{% endapi-method-form-data-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJqdGkiOiJ4MjlRNnIzWkpndVViWHB5RGR0ZVciLCJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJpc3MiOiJodHRwczovL2F1dGhpbmcuY24iLCJpYXQiOjE1NTQ2MTI0NjQsImV4cCI6MTU1NDYxNjA2NCwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBvZmZsaW5lX2FjY2VzcyBwaG9uZSBlbWFpbCIsImF1ZCI6IjVjYTc2NWUzOTMxOTRkNTg5MWRiMTkyNyJ9.VgrdtZRCbapS0hCe5BiV-8rUTXd4x-ZMoFPHV5Zh_HCw-OsJoYN0mVwB1UQ0ZkrA4ojpcZ3MrLnKzRC81BgEnfvaInTqXW8qP36TvR-vl7JkVT-ThkBr0Xdilk0hCfWaMbX9qtCjWYT0b9zxDAdkBKygjztZ74TwKbxNI83vdKSj9A6OfwX9MG4k-Q3ZbKAj1fwncBAp2DEsv1Bd_-4y_n_w-2QtbzZf3409UEotKuU_wGLoVE3DLxJFvEtmunbxQOkqxOGS_JaIvFdhpTZ6I3H_DC5KO8xOR2A6nZGFOhYOZZfnr6tmY_EnOIEsnp4glgTCOqHhd1xoBoDcnEmWEA",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJub25jZSI6IjIyMTIxIiwiYXRfaGFzaCI6InVySTYzZ3hyeU01UzNqejRLMmpWeGciLCJzaWQiOiIxOTdlOGExMy0wMzE4LTRkZDEtYjQ3Mi0xZjI0MDk5ZTUzOWYiLCJhdWQiOiI1Y2E3NjVlMzkzMTk0ZDU4OTFkYjE5MjciLCJleHAiOjE1NTQ2MTYwNjQsImlhdCI6MTU1NDYxMjQ2NCwiaXNzIjoiaHR0cHM6Ly9hdXRoaW5nLmNuIn0.wh3kCIGyu7IHvkbqCeu9OHg9mdLg-wSbU-1UBLPcNxl5MeXsGxtxjPyM6aONxLt_ZXfBFNZM7FWfGpV_qGSNmeGp0UYV_bK-N0wgB5ZkTN1O4EMECqy7qCExwK3kjsOa-o0KkkJxxcDkfEJ3Icn2Nr3q5ozMz_3oGJWqSt0KxQaR_rCtjbLV6dIpPL1MTpWElORXjsoKb1RVOHF0Qpfq8iuGVJAw828tq4cyLH9-IkE9TGX2L6dWmPaY1xd0ho0N1mqnWJrqacljrvX8qPTfGAB9-9rDk2EvFrZkFY6O6bKlMqdyX4ktxYMlku4-H74wxOqkQ_ZWlI3SUG_m-DNDWg",
  "refresh_token": "wlfsGj5oSm5xmdUV_HqS9FTQpaj",
  "scope": "openid profile offline_access phone email",
  "token_type": "Bearer
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

![](../../.gitbook/assets/image%20%283%29.png)

## 使用隐式流程（Implicit Flow）

隐式流程将不获取 code，直接在回调地址中附带 `access_token` 和 `id_token`。

### 在控制台配置 OIDC 应用

选择 `implicit` 模式，并在下方选择 `id_token token` 和 `id_token`。

![](../../.gitbook/assets/image%20%28306%29.png)

### 发起授权

发起授权需要拼接一个用来授权的 URL，具体参数如下：

| 参数名 | 意义 |
| :--- | :--- |
| client\_id | OIDC 应用的 **app\_id** |
| redirect\_uri | 在控制台配置的 OIDC 回调 url 其中的一个值。启用隐式模式时，**控制台配置的所有** redirect\_uri 必须都为 https 协议 |
| scope | 需要请求的权限，如果需要获取 unionid 需要包含 unionid，如果需要获取手机号和 email 需要有 phone email，同时 id\_token 中会包含相关的字段。隐式模式不能刷新 token，所以 offline\_access 字段无效。 |
| response\_type | OIDC 模式，可以为 id\_token, id\_token token [参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationExamples) |
| prompt | 可以为 none，login，consent 或 select\_account，指定 AP 与 End-User 的交互方式。[参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) |
| state | 一个随机字符串，用于防范 CSRF 攻击，如果 response 中的 state 值和发送请求之前设置的 state 值不同，说明受到攻击 |
| nonce | 一个随机字符串，用于防范 Replay 攻击，implicit 模式下必须填 |

假设你创建了一个域名为 `example` 的 OIDC 应用，那么授权网址是：

```text
GET https://example.authing.cn/oauth/oidc/auth?client_id=5ca765e393194d5891db1927&redirect_uri=https://example.com&scope=openid profile&response_type=id_token token&state=jazz&nonce=1831289
```

### 获取 id\_token 和 access\_token

id\_token、access\_token 会以 url **hash** 的形式传递，跳转后链接示例：

```text
https://authing.cn/#id_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1Y2QwMjZlZjNlZDlmOTRkODBmZTM2YWUiLCJub25jZSI6IjE4MzEyODkiLCJzaWQiOiI4YzgzN2I5My01OTNlLTQzZjctYWMzNC0yYjRmZDU3ZGFhMTciLCJhdF9oYXNoIjoiVFFtbFlEVTVPZGF1Zjl0U0VKdHY5USIsInNfaGFzaCI6Ind3SDNXclV2b0hiSUp5TWVZVHU4bHciLCJhdWQiOiI1ZDAxZTM4OTk4NWY4MWM2YzFkZDMxZGUiLCJleHAiOjE1NjA0MDkzNjgsImlhdCI6MTU2MDQwNTc2OCwiaXNzIjoiaHR0cHM6Ly9vYXV0aC5hdXRoaW5nLmNuL29hdXRoL29pZGMifQ.T9M0s6rk4Teq6VOOBRIElgHK9KyM3q0ZJj2aS0VD_Fw&access_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI3OE9XcVJNVXJEUXpMMXpHVzVtUWoiLCJzdWIiOiI1Y2QwMjZlZjNlZDlmOTRkODBmZTM2YWUiLCJpc3MiOiJodHRwczovL29hdXRoLmF1dGhpbmcuY24vb2F1dGgvb2lkYyIsImlhdCI6MTU2MDQwNTc2OCwiZXhwIjoxNTYwNDA5MzY4LCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIiwiYXVkIjoiNWQwMWUzODk5ODVmODFjNmMxZGQzMWRlIn0.mR0MZDwlZWGRMsAZjQ27sDFFqYoDgZ6WHTK4C7JbML4&expires_in=3600&token_type=Bearer&state=jazz&session_state=26ec053be9f47d68dc430f84b97efb1095469fe10169a9e00ef4092718714b8b
```

换取用户信息的流程和授权码模式相同。

参考资料：

1. [scope 与用户信息对应表](https://docs.authing.cn/authing/advanced/oidc/oidc-params#scope-can-shu-dui-ying-de-yong-hu-xin-xi)

## 使用混合模式（Hybrid Flow）

混合模式直接在回调地址中附带 `code`、`access_token` 和 `id_token`，且都以 URL **Hash** 的形式传递。

### 在控制台配置 OIDC 应用

选择 `authorization_code` 和 `implicit`，并在下方勾选 `code id_token token` 、`code id_token` 、`code token`。

![](../../.gitbook/assets/image%20%28135%29.png)

### 发起授权

发起授权需要拼接一个用来授权的 URL，具体参数如下：

| 参数名 | 意义 |
| :--- | :--- |
| client\_id | OIDC 应用的 **app\_id** |
| redirect\_uri | 在控制台配置的 OIDC 回调 url 其中的一个值。启用隐式模式时，**控制台配置的所有** redirect\_uri 必须都为 https 协议 |
| scope | 需要请求的权限，如果需要获取 unionid 需要包含 unionid，如果需要获取手机号和 email 需要有 phone email，同时 id\_token 中会包含相关的字段。如果需要刷新 token，需要有 offline\_access 参数，同时 response\_type 参数中必须包含 code，并使用 code 换取 token，否则 offline\_access 字段无效。 |
| response\_type | OIDC 模式，此处为 code id\_token token [参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationExamples) |
| prompt | 可以为 none，login，consent 或 select\_account，指定 AP 与 End-User 的交互方式。[参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) |
| state | 一个随机字符串，用于防范 CSRF 攻击，如果 response 中的 state 值和发送请求之前设置的 state 值不同，说明受到攻击 |
| nonce | 一个随机字符串，用于防范 Replay 攻击，混合模式下必填 |

假设你创建了一个域名为 `example` 的 OIDC 应用，那么授权网址是：

```text
GET https://example.authing.cn/oauth/oidc/auth?client_id=5ca765e393194d5891db1927&redirect_uri=https://example.com&scope=openid profile&response_type=code id_token token&state=jazz&nonce=1831289
```

跳转后链接示例：

```text
https://example.com/#code=pIY83Jl_bcerNN9Wt57Sq0TAjTr&id_token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJub25jZSI6IjE4MzEyODkiLCJzaWQiOiIxOTdlOGExMy0wMzE4LTRkZDEtYjQ3Mi0xZjI0MDk5ZTUzOWYiLCJhdF9oYXNoIjoiUFlXaTFER29jRlotYmlYd0d5WXlpZyIsImNfaGFzaCI6Ik4yUmkyUFpidktYdXRmdGhZbUhrM2ciLCJzX2hhc2giOiJ3d0gzV3JVdm9IYklKeU1lWVR1OGx3IiwiYXVkIjoiNWNhNzY1ZTM5MzE5NGQ1ODkxZGIxOTI3IiwiZXhwIjoxNTU0NjE1NjcyLCJpYXQiOjE1NTQ2MTIwNzIsImlzcyI6Imh0dHBzOi8vYXV0aGluZy5jbiJ9.a--JC_6CyUi0Z7z3DCKT51wJkKT7MmtlVHhrNujhxHCfgQqzqS3wMxVj6oEe_cfjVQNgJ-Xe1oiL8uMAxVN-cM1Ra1JQcavUujua2IxxtG4Nkh84rTukqsrPfuNhNO7MRP6Fa9qIIdKeKkQKyh1zBKE6322zK_ECdfGd2sWdqqXiQyJXg6ODhPZDidsGuluV3bZiAY3brMSMmh6QC99StOP5ZwSKtlRMyYE3MIRWsQ4W2HkHBrk67T_scQ6XN6mdBKi2OZW-E7fXeyVwH-ibWDzlUpmFSaj3a-WbkDe3nfCv8MHj439aJNU-AXfIgLsckvCO5_dJOUWGHg6hemT9bw&access_token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJqdGkiOiIxUzgyaUtSdXFlWW1DUmFrMFl1S0kiLCJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJpc3MiOiJodHRwczovL2F1dGhpbmcuY24iLCJpYXQiOjE1NTQ2MTIwNzIsImV4cCI6MTU1NDYxNTY3Miwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSIsImF1ZCI6IjVjYTc2NWUzOTMxOTRkNTg5MWRiMTkyNyJ9.tHwxiH5QXXA46Y4mIwcBck3uDArMj5TMGEBAQ8Eeln6oFbwBY3aS5cSV6e3anZDwKZrdgrdFlyj9-Bl1T5V1rNJK-Xz_aFnM6XxyO1jSHcn-6KXGwmz68D50VIHior39cuoj9OXbNCei5RVghjh2cRT3SenYki7UeJBgmfQA6l2aZZpBrn9aphXr9OoPS47T59I0Ynn2yMIYIMDOX7hh8E5oV1hrK3hyjAvp3ghmzyRfj2BlG9rBo1hd_d5E8x6OIzNdvPKXwVASJZRxov2Dx0ma36zxzSObyXgCloUv2KlbmL9-Wj8d3H6FhHC75DLfJYx-uRgNqW7CFKGeRkPjkQ&expires_in=3600&token_type=Bearer&state=jazz&session_state=101666b6b70cfb4406ad9c0c906039de39776140e66e48acdb63ab8acb309701
```

换取用户信息的流程和授权码模式相同。

参考资料：

1. [scope 与用户信息对应表](https://docs.authing.cn/authing/advanced/oidc/oidc-params#scope-can-shu-dui-ying-de-yong-hu-xin-xi)

## 退出 SSO

如果你使用了 OAuth、OIDC 或 SAML 实现了单点登录，那么使用户退出登录需要跳转到一个 URL：

> https://&lt;你的域名&gt;.authing.cn/login/profile/logout?app\_id=&lt;OAuth 应用 ID&gt;&redirect\_uri=&lt;退出之后的回调地址&gt;

其中 `app_id` 和 `redirect_uri` 都是必填选项，`redirect_uri` 是退出后你想要返回的地址。

如果你想要在后端退出 SSO，可以自行维护一个 Cookie - Session 的状态，然后设置 Cookie 过期即可。

## 接下来你可能需要

{% page-ref page="oidc-params.md" %}

