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

![](../../.gitbook/assets/image%20%28340%29.png)

### 02 - 发起登录请求

{% api-method method="get" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/auth" %}
{% api-method-summary %}
 发起 OIDC 登录请求并让终端用户在浏览器中访问
{% endapi-method-summary %}

{% api-method-description %}
发起授权需要拼接一个用来授权的 URL，并让终端用户在浏览器中访问，具体参数如下：
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
需要请求的权限，必须包含 **openid**。如果需要获取 unionid 需要包含 unionid；如果需要**获取手机号**和 **email** 需要包含 phone email；如果需要用户与 Authing 之间的 Token 需要包含 authing\_token；如果需要 refresh\_token 需要包含 offline\_access。多个 scope 请用**空格分隔**。同时 **id\_token** 中会包含**相关的字段**。
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

![](../../.gitbook/assets/image%20%2881%29.png)



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
OIDC 应用的 App ID
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

使用 curl 发送请求示例：

```text
curl --location --request POST 'https://core.authing.cn/oauth/oidc/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'code=61yhuOVrgyhKlFTU~bnEKA_fnnz' \
--data-urlencode 'client_id=5e37979f7b757ead14c534af' \
--data-urlencode 'client_secret=64b517f8de3648091654eb4ee9b479d3' \
--data-urlencode 'grant_type=authorization_code' \
--data-urlencode 'redirect_uri=https://baidu.com'
```

返回示例：

```text
{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IjEifQ.eyJqdGkiOiJQZU41YXg1b3FabGRhcUJUMzQzeUkiLCJzdWIiOiI1Y2U1M2FlYTlmODUyNTdkZDEzMmQ3NDkiLCJpc3MiOiJodHRwczovL29hdXRoLmF1dGhpbmcuY24vb2F1dGgvb2lkYyIsImlhdCI6MTU4MTQyMDk1NywiZXhwIjoxNTgxNDI0NTU0LCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIGF1dGhpbmdfdG9rZW4gZW1haWwgcGhvbmUgYWRkcmVzcyBvZmZsaW5lX2FjY2VzcyIsImF1ZCI6IjVkMDFlMzg5OTg1ZjgxYzZjMWRkMzFkZSJ9.rtpRSL3_U03zXShZUCILquSR_KEDuS-OldWpy8RLztWUNG_tMyrg2g9CG4hC7pJUwmgzZKtp7vsVrj6W0eyo_ehE4KGz9iKnyd46DFbx9W9pi-mieRW5HuVMGL2zvDH8zF467WXET2SVB3LUhFLNmEbxpvjPZ5Ksvbcd7nqHfnUN4-z3SqAvhGWWfcmt7QDFlLtWPw4LzyznEqmM9sDkNiNDnTkjmcjm7yHJR-yv5FvpzQB2kraQVOrrdAixbHf29ihOVO25CrjmgeKemg1vuLNGUcOrr_XWn7xaCSvyAfXrBuRalecW9RA4p_Cp6YslHc_572awekt3kUO2TebUQA",
    "expires_in": 3597,
    "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IjEifQ.eyJzdWIiOiI1Y2U1M2FlYTlmODUyNTdkZDEzMmQ3NDkiLCJiaXJ0aGRhdGUiOiIiLCJmYW1pbHlfbmFtZSI6IiIsImdlbmRlciI6IiIsImdpdmVuX25hbWUiOiIiLCJsb2NhbGUiOiIiLCJtaWRkbGVfbmFtZSI6IiIsIm5hbWUiOiIiLCJuaWNrbmFtZSI6IiIsInBpY3R1cmUiOiJodHRwczovL3VzZXJjb250ZW50cy5hdXRoaW5nLmNuL2F1dGhpbmctYXZhdGFyLnBuZyIsInByZWZlcnJlZF91c2VybmFtZSI6IiIsInByb2ZpbGUiOiIiLCJ1cGRhdGVkX2F0IjoiIiwid2Vic2l0ZSI6IiIsInpvbmVpbmZvIjoiIiwiY29tcGFueSI6IiIsImJyb3dzZXIiOiIiLCJsb2dpbnNfY291bnQiOjEwMywicmVnaXN0ZXJfbWV0aG9kIjoiZGVmYXVsdDp1c2VybmFtZS1wYXNzd29yZCIsImJsb2NrZWQiOmZhbHNlLCJsYXN0X2lwIjoiMTIxLjIxLjU2LjE3MSIsInJlZ2lzdGVyX2luX3VzZXJwb29sIjoiNWM5NTkwNTU3OGZjZTUwMDAxNjZmODUzIiwibGFzdF9sb2dpbiI6IjIwMjAtMDItMTFUMTE6MzU6MTUuNjk2WiIsInNpZ25lZF91cCI6IjIwMTktMDUtMjJUMTI6MDQ6NTguMjk0WiIsInRva2VuIjoiZXlKaGJHY2lPaUpJVXpJMU5pSXNJblI1Y0NJNklrcFhWQ0o5LmV5SmtZWFJoSWpwN0ltVnRZV2xzSWpvaWRHVnpkRE5BTVRJekxtTnZiU0lzSW1sa0lqb2lOV05sTlROaFpXRTVaamcxTWpVM1pHUXhNekprTnpRNUlpd2lZMnhwWlc1MFNXUWlPaUkxWXprMU9UQTFOVGM0Wm1ObE5UQXdNREUyTm1ZNE5UTWlmU3dpYVdGMElqb3hOVGd4TkRJd09URTFMQ0psZUhBaU9qRTFPREkzTVRZNU1URjkuM0l0X0NJQTNFbUpoYWcyMW92WjNwd0RfY0owcTVTZkJjSURSZThRX3FoayIsImVtYWlsIjoidGVzdDNAMTIzLmNvbSIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwicGhvbmVfbnVtYmVyIjoiMTMxMTIzNDEyMzQiLCJhZGRyZXNzIjoiIiwiYXRfaGFzaCI6IjV6QnNUOHF4RHc1RmNYdU55UFg4YUEiLCJzaWQiOiJkNmZiOTE5Ny00NmE3LTQ1ZGEtOGVkMC05ODhjZjg0ZjQwZWUiLCJhdWQiOiI1ZDAxZTM4OTk4NWY4MWM2YzFkZDMxZGUiLCJleHAiOjE1ODE0MjQ1NTQsImlhdCI6MTU4MTQyMDk1NywiaXNzIjoiaHR0cHM6Ly9vYXV0aC5hdXRoaW5nLmNuL29hdXRoL29pZGMifQ.VZzqULytIteyBfouww5TsHQ50gEhM06kUWMeDiO3FVFSCW9ys2bFPos5p6LFzliK4Ce09ypOwVQiRnE2gNYsukLvlUPlKDIP_Xk5W19frKi1Z8ImuIPvUqVMKbFutVNS0TfIPCPJVBl8C1j5OXeIs6z0V90QrvyJao6FqVEa3axOHxbhpo1fH2hP04-wkGOp_l10d7RFhGcnPyPnz9-C5X6A4UEsCSDCVw1mDQHxDSFP9OPaB_OlCG_Bi6G-CeLhPa3V5hyIefdBvxC9SIpK-6qY-_BfsNKkBHDVKMb0xodgN2hzn3UTUGBuuoiaB4JhCv72EZ7eiXKIXFz6zVcogA",
    "refresh_token": "DuSPlrUFPAvCZ1WQKarv5MbEsXN",
    "scope": "openid profile authing_token email phone address offline_access",
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
  "company": "",
  "browser": "",
  "logins_count": 103,
  "register_method": "default:username-password",
  "blocked": false,
  "last_ip": "121.21.56.171",
  "register_in_userpool": "5c95905578fce5000166f853",
  "last_login": "2020-02-11T11:35:15.696Z",
  "signed_up": "2019-05-22T12:04:58.294Z",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoidGVzdDNAMTIzLmNvbSIsImlkIjoiNWNlNTNhZWE5Zjg1MjU3ZGQxMzJkNzQ5IiwiY2xpZW50SWQiOiI1Yzk1OTA1NTc4ZmNlNTAwMDE2NmY4NTMifSwiaWF0IjoxNTgxNDIwOTE1LCJleHAiOjE1ODI3MTY5MTF9.3It_CIA3EmJhag21ovZ3pwD_cJ0q5SfBcIDRe8Q_qhk",
  "email": "test3@123.com",
  "email_verified": false,
  "phone_number": "13112341234",
  "address": "",
  "at_hash": "5zBsT8qxDw5FcXuNyPX8aA",
  "sid": "d6fb9197-46a7-45da-8ed0-988cf84f40ee",
  "aud": "5d01e389985f81c6c1dd31de",
  "exp": 1581424554,
  "iat": 1581420957,
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

返回示例：

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
    "company": "",
    "browser": "",
    "logins_count": 103,
    "register_method": "default:username-password",
    "blocked": false,
    "last_ip": "127.0.0.1",
    "register_in_userpool": "5c95905578fce5000166f853",
    "last_login": "2020-02-11T11:35:15.696Z",
    "signed_up": "2019-05-22T12:04:58.294Z",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoidGVzdDNAMTIzLmNvbSIsImlkIjoiNWNlNTNhZWE5Zjg1MjU3ZGQxMzJkNzQ5IiwiY2xpZW50SWQiOiI1Yzk1OTA1NTc4ZmNlNTAwMDE2NmY4NTMifSwiaWF0IjoxNTgxNDIwOTE1LCJleHAiOjE1ODI3MTY5MTF9.3It_CIA3EmJhag21ovZ3pwD_cJ0q5SfBcIDRe8Q_qhk",
    "email": "test3@123.com",
    "email_verified": false,
    "phone_number": "13112341234",
    "phone_number_verified": false,
    "address": ""
}
```

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

![](../../.gitbook/assets/image%20%28193%29.png)

## 使用隐式流程（Implicit Flow）

隐式流程将不获取 code，直接在回调地址中附带 `access_token` 和 `id_token`。

### 在控制台配置 OIDC 应用

选择 `implicit` 模式，并在下方选择 `id_token token` 和 `id_token`。

![](../../.gitbook/assets/image%20%28389%29.png)

### 发起授权

发起授权需要拼接一个用来授权的 URL，并让终端用户在浏览器中访问，具体参数如下：

| 参数名 | 意义 |
| :--- | :--- |
| client\_id | OIDC 应用的 **app\_id** |
| redirect\_uri | 在控制台配置的 OIDC 回调 url 其中的一个值。启用隐式模式时，**控制台配置的所有** redirect\_uri 必须都为 https 协议 |
| scope | 需要请求的权限，必须包含 **openid**。如果需要获取 unionid 需要包含 unionid；如果需要**获取手机号**和 **email** 需要包含 phone email；如果需要用户与 Authing 之间的 Token 需要包含 authing\_token；多个 scope 请用**空格分隔**。同时 id\_token 中会包含相关的字段。**隐式模式不能刷新 token**，所以 offline\_access 字段无效。 |
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

![](../../.gitbook/assets/image%20%28219%29.png)

### 发起授权

发起授权需要拼接一个用来授权的 URL，并让终端用户在浏览器中访问，具体参数如下：

| 参数名 | 意义 |
| :--- | :--- |
| client\_id | OIDC 应用的 **app\_id** |
| redirect\_uri | 在控制台配置的 OIDC 回调 url 其中的一个值。启用隐式模式时，**控制台配置的所有** redirect\_uri 必须都为 https 协议 |
| scope | 需要请求的权限，必须包含 **openid**。如果需要获取 unionid 需要包含 unionid，如果需要**获取手机号**和 **email** 需要包含 phone email，如果需要用户与 Authing 之间的 Token 需要包含 authing\_token。多个 scope 请用空格分开。同时 **id\_token** 中会包含相关的字段。如果需要刷新 token，需要有 offline\_access 参数，同时 **response\_type** 参数中**必须包含 code**，**并使用 code 换取 token**，否则 offline\_access 字段无效。 |
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

## 使用 Password 模式

### 在控制台配置 OIDC 应用

在[**控制台**](https://authing.cn/dashboard) &gt; **第三方登录** &gt; **OIDC 应用**，打开 OIDC 应用详情，在表单的**授权模式**中勾选「password」。

![&#x5F00;&#x542F; Password &#x6A21;&#x5F0F;](https://cdn.authing.cn/docs/20200403105822.png)

### 发起授权

{% api-method method="post" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/token" %}
{% api-method-summary %}
使用登录凭据换取 token
{% endapi-method-summary %}

{% api-method-description %}
在 Password 模式中，可以直接使用用户的登录凭据换取 OIDC token
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="scope" type="string" required=false %}
授权域，一个字符串，openid 为必选，可选值有 profile email address phone offline\_access。不同的值之间用空格分开。profile 是用户基本信息，email 是用户邮箱，address 是用户地址，phone 是用户手机号，offline\_access 会返回 refresh\_token，用于后续刷新 OIDC Token。默认为 openid profile。
{% endapi-method-parameter %}

{% api-method-parameter name="password" type="string" required=false %}
密码，当没有填写 unionid 时必填。
{% endapi-method-parameter %}

{% api-method-parameter name="unionid" type="string" required=false %}
三方身份提供商身份 ID，与手机号、邮箱、用户名互斥。
{% endapi-method-parameter %}

{% api-method-parameter name="phone" type="string" required=false %}
手机号，与邮箱、用户名、unionid 互斥。
{% endapi-method-parameter %}

{% api-method-parameter name="email" type="string" required=false %}
邮箱，与用户名、手机号、unionid 互斥。
{% endapi-method-parameter %}

{% api-method-parameter name="username" type="string" required=false %}
用户名，与邮箱、手机号、unionid 互斥。
{% endapi-method-parameter %}

{% api-method-parameter name="grant\_type" type="string" required=true %}
必须填 `password`。
{% endapi-method-parameter %}

{% api-method-parameter name="client\_secret" type="string" required=true %}
OIDC 应用 secret。
{% endapi-method-parameter %}

{% api-method-parameter name="client\_id" type="string" required=true %}
OIDC 应用 id。
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
用户登录凭证正确，返回 OIDC 相关 Token。
{% endapi-method-response-example-description %}

```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IlJJQ015UV9yVFFESlZGYkJEclBDdml0ME9LczBSNWNRX3N1Vmt4alRmOUkifQ.eyJqdGkiOiJXWEl3TldJZ3ZCZ0tXN0lncnVzdXQiLCJzdWIiOiI1ZTg1YWRlZDljYzE4ZTRjYzY3ZTc4MGEiLCJpc3MiOiJodHRwczovL3Rlc3Q4OC5jZWxlYmVzLmxpdmUvb2F1dGgvb2lkYyIsImlhdCI6MTU4NTgxOTExOCwiZXhwIjoxNTg1ODIyNzE4LCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIGVtYWlsIHBob25lIG9mZmxpbmVfYWNjZXNzIiwiYXVkIjoiNWU1ZmMzNGZmMTRiYjI1OTkyZWUyNzgxIn0.Tk4npueRIyJPVxrfiOVQgMKEbk4z_KOvOkOGNyVKyeMtSHEcvU8tMRhjBP_ZrISaT4XO-vu_O1tDUBFhCKMDXWnPMwNotbbcRwRdmzueoe1G0YyxHvcuNihAFyfT_99Skaq3TuG7EzeeFuuvkUejFKRmaODraQY1vQrJl_0WNX1f6NZVYNUcOTCslb_R6qNodFQvjfOJv73FyArETKRAKN5sdTtUWuwxf9QfNm5jwJ_iratqSb5GYU-hd6U-47JKzqv_NEEVrGcRSDrW4ICrulOVPduKOwUqwg7VjHqpvAk2cIt5UdgSh2aaj3KpBhRWm2Exp2AY62sP-oLU3qigBQ",
    "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IlJJQ015UV9yVFFESlZGYkJEclBDdml0ME9LczBSNWNRX3N1Vmt4alRmOUkifQ.eyJzdWIiOiI1ZTg1YWRlZDljYzE4ZTRjYzY3ZTc4MGEiLCJiaXJ0aGRhdGUiOiIiLCJmYW1pbHlfbmFtZSI6IiIsImdlbmRlciI6IiIsImdpdmVuX25hbWUiOiIiLCJsb2NhbGUiOiIiLCJtaWRkbGVfbmFtZSI6IiIsIm5hbWUiOiIiLCJuaWNrbmFtZSI6IiIsInBpY3R1cmUiOiJodHRwczovL3VzZXJjb250ZW50cy5hdXRoaW5nLmNuL2F1dGhpbmctYXZhdGFyLnBuZyIsInByZWZlcnJlZF91c2VybmFtZSI6IiIsInByb2ZpbGUiOiIiLCJ1cGRhdGVkX2F0IjoiIiwid2Vic2l0ZSI6IiIsInpvbmVpbmZvIjoiIiwidXNlcm5hbWUiOiJwNGdnMnVhcWRjZUB0ZXN0LmNvbSIsImNvbXBhbnkiOiIiLCJicm93c2VyIjoiIiwiZGV2aWNlIjoiIiwibG9naW5zX2NvdW50IjoxLCJyZWdpc3Rlcl9tZXRob2QiOiJkZWZhdWx0OnVzZXJuYW1lLXBhc3N3b3JkIiwiYmxvY2tlZCI6ZmFsc2UsImxhc3RfaXAiOiIxMjQuMjA0LjU2Ljk4IiwicmVnaXN0ZXJfaW5fdXNlcnBvb2wiOiI1ZTE5OTQyMTg4YjAxMzA3ODEyN2MwMjQiLCJsYXN0X2xvZ2luIjoiMjAyMC0wNC0wMlQwOToxODozNy4zNDJaIiwic2lnbmVkX3VwIjoiMjAyMC0wNC0wMlQwOToxODozNy4xODFaIiwiZW1haWwiOiJwNGdnMnVhcWRjZUB0ZXN0LmNvbSIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwicGhvbmVfbnVtYmVyIjoiIiwicGhvbmVfbnVtYmVyX3ZlcmlmaWVkIjpmYWxzZSwiYXdzIjoiZGVtbyIsImF1ZCI6IjVlNWZjMzRmZjE0YmIyNTk5MmVlMjc4MSIsImV4cCI6MTU4NTgyMjcxOCwiaWF0IjoxNTg1ODE5MTE4LCJpc3MiOiJodHRwczovL3Rlc3Q4OC5jZWxlYmVzLmxpdmUvb2F1dGgvb2lkYyJ9.XUOWiKi0mpkcw570rCBZVz0wHWz1gBi5N5Bgz8mqU08FA2dlFradoZ9m_pZYlZPHW6A9R54rI7MzONQlt-sDjGqxLkL6wSNrYIJuYlysRldK3E1NRmziVukMQn8jkyq1DLhKK3WzX_ODbkasHTxdFmJ6iAgouuTjdCdcGv1B1ZTXIKJoIgXwMnYjrWbDULkJg_5_o7eP1GCVG8l5UgIRy5YNunEg7nEVLAu0aj-ob613x5k7ceb-jYLjCX2_9PVIEDeE5exGbz-3txhAxn77xjTi7m1-NEhusTHhd_p315fs0ziCYCaXXsO9eRlJ7I78geP87Thq3-vgQH7YgNy8tA",
    "refresh_token": "vRZXKWcvyVE9_kKq26OD2gFyame",
    "expires_in": 3600,
    "token_type": "Bearer",
    "scope": "openid profile email phone offline_access"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
用户登录凭证错误，返回错误信息。
{% endapi-method-response-example-description %}

```
{
    error: 'invalid_grant',
    error_description: 'invalid credentials provided',
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## 使用 authingToken 模式 <a id="use-authing-token-mode"></a>

### 在控制台配置 OIDC 应用 <a id="configure-oidc-app-in-dashboard"></a>

在[**控制台**](https://authing.cn/dashboard) &gt; **第三方登录** &gt; **OIDC 应用**，打开 OIDC 应用详情，在表单的**授权模式**中勾选「authingToken」。

![&#x5F00;&#x542F; authingToken &#x6388;&#x6743;&#x6A21;&#x5F0F;](https://cdn.authing.cn/docs/20200403121146.png)

### 发起授权

{% api-method method="post" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/token" %}
{% api-method-summary %}
使用登录凭据换取 token
{% endapi-method-summary %}

{% api-method-description %}
在 authingToken 模式中，可以直接使用用户的 Authing Token 换取 OIDC token。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="scope" type="string" required=false %}
授权域，一个字符串，openid 为必选，可选值有 profile email address phone offline\_access。不同的值之间用**空格分开**。profile 是用户基本信息，email 是**用户邮箱**，address 是用户地址，phone 是**用户手机号**，offline\_access 会返回 **refresh\_token**，用于后续刷新 OIDC Token。默认为 openid profile。
{% endapi-method-parameter %}

{% api-method-parameter name="authingToken" type="string" required=true %}
用户的 Authing Token。
{% endapi-method-parameter %}

{% api-method-parameter name="grant\_type" type="string" required=true %}
必须填 `authingToken`。
{% endapi-method-parameter %}

{% api-method-parameter name="client\_secret" type="string" required=true %}
OIDC 应用 secret。
{% endapi-method-parameter %}

{% api-method-parameter name="client\_id" type="string" required=true %}
OIDC 应用 id。
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
用户登录凭证正确，返回 OIDC 相关 Token。
{% endapi-method-response-example-description %}

```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IlJJQ015UV9yVFFESlZGYkJEclBDdml0ME9LczBSNWNRX3N1Vmt4alRmOUkifQ.eyJqdGkiOiJXWEl3TldJZ3ZCZ0tXN0lncnVzdXQiLCJzdWIiOiI1ZTg1YWRlZDljYzE4ZTRjYzY3ZTc4MGEiLCJpc3MiOiJodHRwczovL3Rlc3Q4OC5jZWxlYmVzLmxpdmUvb2F1dGgvb2lkYyIsImlhdCI6MTU4NTgxOTExOCwiZXhwIjoxNTg1ODIyNzE4LCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIGVtYWlsIHBob25lIG9mZmxpbmVfYWNjZXNzIiwiYXVkIjoiNWU1ZmMzNGZmMTRiYjI1OTkyZWUyNzgxIn0.Tk4npueRIyJPVxrfiOVQgMKEbk4z_KOvOkOGNyVKyeMtSHEcvU8tMRhjBP_ZrISaT4XO-vu_O1tDUBFhCKMDXWnPMwNotbbcRwRdmzueoe1G0YyxHvcuNihAFyfT_99Skaq3TuG7EzeeFuuvkUejFKRmaODraQY1vQrJl_0WNX1f6NZVYNUcOTCslb_R6qNodFQvjfOJv73FyArETKRAKN5sdTtUWuwxf9QfNm5jwJ_iratqSb5GYU-hd6U-47JKzqv_NEEVrGcRSDrW4ICrulOVPduKOwUqwg7VjHqpvAk2cIt5UdgSh2aaj3KpBhRWm2Exp2AY62sP-oLU3qigBQ",
    "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IlJJQ015UV9yVFFESlZGYkJEclBDdml0ME9LczBSNWNRX3N1Vmt4alRmOUkifQ.eyJzdWIiOiI1ZTg1YWRlZDljYzE4ZTRjYzY3ZTc4MGEiLCJiaXJ0aGRhdGUiOiIiLCJmYW1pbHlfbmFtZSI6IiIsImdlbmRlciI6IiIsImdpdmVuX25hbWUiOiIiLCJsb2NhbGUiOiIiLCJtaWRkbGVfbmFtZSI6IiIsIm5hbWUiOiIiLCJuaWNrbmFtZSI6IiIsInBpY3R1cmUiOiJodHRwczovL3VzZXJjb250ZW50cy5hdXRoaW5nLmNuL2F1dGhpbmctYXZhdGFyLnBuZyIsInByZWZlcnJlZF91c2VybmFtZSI6IiIsInByb2ZpbGUiOiIiLCJ1cGRhdGVkX2F0IjoiIiwid2Vic2l0ZSI6IiIsInpvbmVpbmZvIjoiIiwidXNlcm5hbWUiOiJwNGdnMnVhcWRjZUB0ZXN0LmNvbSIsImNvbXBhbnkiOiIiLCJicm93c2VyIjoiIiwiZGV2aWNlIjoiIiwibG9naW5zX2NvdW50IjoxLCJyZWdpc3Rlcl9tZXRob2QiOiJkZWZhdWx0OnVzZXJuYW1lLXBhc3N3b3JkIiwiYmxvY2tlZCI6ZmFsc2UsImxhc3RfaXAiOiIxMjQuMjA0LjU2Ljk4IiwicmVnaXN0ZXJfaW5fdXNlcnBvb2wiOiI1ZTE5OTQyMTg4YjAxMzA3ODEyN2MwMjQiLCJsYXN0X2xvZ2luIjoiMjAyMC0wNC0wMlQwOToxODozNy4zNDJaIiwic2lnbmVkX3VwIjoiMjAyMC0wNC0wMlQwOToxODozNy4xODFaIiwiZW1haWwiOiJwNGdnMnVhcWRjZUB0ZXN0LmNvbSIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwicGhvbmVfbnVtYmVyIjoiIiwicGhvbmVfbnVtYmVyX3ZlcmlmaWVkIjpmYWxzZSwiYXdzIjoiZGVtbyIsImF1ZCI6IjVlNWZjMzRmZjE0YmIyNTk5MmVlMjc4MSIsImV4cCI6MTU4NTgyMjcxOCwiaWF0IjoxNTg1ODE5MTE4LCJpc3MiOiJodHRwczovL3Rlc3Q4OC5jZWxlYmVzLmxpdmUvb2F1dGgvb2lkYyJ9.XUOWiKi0mpkcw570rCBZVz0wHWz1gBi5N5Bgz8mqU08FA2dlFradoZ9m_pZYlZPHW6A9R54rI7MzONQlt-sDjGqxLkL6wSNrYIJuYlysRldK3E1NRmziVukMQn8jkyq1DLhKK3WzX_ODbkasHTxdFmJ6iAgouuTjdCdcGv1B1ZTXIKJoIgXwMnYjrWbDULkJg_5_o7eP1GCVG8l5UgIRy5YNunEg7nEVLAu0aj-ob613x5k7ceb-jYLjCX2_9PVIEDeE5exGbz-3txhAxn77xjTi7m1-NEhusTHhd_p315fs0ziCYCaXXsO9eRlJ7I78geP87Thq3-vgQH7YgNy8tA",
    "refresh_token": "vRZXKWcvyVE9_kKq26OD2gFyame",
    "expires_in": 3600,
    "token_type": "Bearer",
    "scope": "openid profile email phone offline_access"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
用户登录凭证错误，返回错误信息。
{% endapi-method-response-example-description %}

```
{
    error: 'invalid_grant',
    error_description: 'invalid credentials provided',
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## 退出 SSO

如果你使用了 OAuth、OIDC 或 SAML 实现了单点登录，那么使用户退出登录需要跳转到一个 URL：

> https://&lt;你的域名&gt;.authing.cn/login/profile/logout?app\_id=&lt;OAuth 应用 ID&gt;&redirect\_uri=&lt;退出之后的回调地址&gt;

其中 `app_id` 和 `redirect_uri` 都是必填选项，`redirect_uri` 是退出后你想要返回的地址。

如果你想要在后端退出 SSO，可以自行维护一个 Cookie - Session 的状态，然后设置 Cookie 过期即可。

## 接下来你可能需要

{% page-ref page="oidc-params.md" %}

