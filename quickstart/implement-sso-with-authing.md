---
description: >-
  单点登录（Single Sign On），简称为 SSO，是目前比较流行的企业业务整合的解决方案之一。 SSO
  的定义是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。
---

# 实现单点登录

## 开始之前 <a id="before-start"></a>

如果你不了解用户池、单点登录和认证授权，建议先阅读[基础概念](https://docs.authing.cn/authing/quickstart/basic)。

### 预备知识 <a id="prepare"></a>

1. 基本的 HTML 和 CSS 知识
2. 中级 JavaScript 技能

### 所需工具 <a id="tools"></a>

1. 你喜欢的文本编辑器
2. 可以在本地运行的 Web 服务器（比如：`npm install http-server -g`）

## 注册一个 Authing 账号 <a id="register-authing-account"></a>

{% page-ref page="create-authing-account.md" %}

## 创建一个授权应用 <a id="add-oidc-app"></a>

{% hint style="info" %}
若你是首次注册 Authing，可略过此步骤，首次注册时已自动完成此步骤。
{% endhint %}

选择**第三方登录** -&gt; **OIDC 应用**选项卡，点击「创建 OIDC 应用」按钮创建应用。

![](../.gitbook/assets/image%20%28412%29.png)

点击按钮后会弹出一个创建表单，如下图所示：

![](../.gitbook/assets/image%20%28577%29.png)

**填写应用名称**，并指定此应用的二级域名（用户将通过此网址进行认证）和回调地址（业务地址），其他参数保留默认，然后点击「确定」。 

**参数解释**

**认证地址**，一个 authing.cn 的二级域名，用户将在此网址进行登录。

**回调 URL**，登录成功后，回调到开发者自己业务的地址。本教程为演示，填写的地址是 http://localhost:8080，实际场景下请填写自己的业务地址。

在应用列表中点击刚创建好的应用，记录下 AppID，二级域名，供以后使用。

## 使用 AuthingSSO SDK 集成单点登录 <a id="use-authing-sso-sdk"></a>

### 创建一个空白的 HTML 文档用来编写 Authing 程序 <a id="add-empty-html"></a>

本教程只是为了演示，因此我们没选择高级框架，这可以让我们专注于 Authing 本身。

```markup
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Authing SSO Example</title>
  </head>
  <body></body>
</html>
```

### 添加三个按钮 <a id="add-three-button"></a>

增加三个按钮控件到 body 中，目的是为了演示如何使用 SDK 管理单点登录状态。

```markup
<button id="btn-login">login</button>
<button id="btn-track-session">trackSession</button>
<button id="btn-logout">logout</button>
```

### 引入 AuthingSSO 并初始化 <a id="init-authing-sso"></a>

从 CDN 加载 [AuthingSSO](https://github.com/Authing/AuthingSSO) 的 SDK。填入你的 OIDC 应用 ID 和 域名，进行初始化。

```markup
<script src="https://cdn.jsdelivr.net/npm/@authing/sso/dist/AuthingSSO.umd.min.js"></script>
<script>
  let auth = new AuthingSSO({
    appId: "YOUR_OIDC_APP_ID",
    appType: "oidc",
    appDomain: "YOUR_OIDC_APP_DOMAIN.authing.cn"
  });
</script>
```

### 监听按钮的点击事件 <a id="listen-click-event"></a>

达到的效果是：

* 点击 login 按钮，浏览器会跳转到登录页面，与用户完成身份确认。
* 点击 trackSession 按钮，会显示当前登录状态。
* 点击 logout 按钮，进行单点登出。

```javascript
let login = document.getElementById("btn-login");
let trackSession = document.getElementById("btn-track-session");
let logout = document.getElementById("btn-logout");
login.onclick = function() {
  auth.login();
};
trackSession.onclick = async function() {
  let res = await auth.trackSession();
  alert(JSON.stringify(res));
};
logout.onclick = async function() {
  let res = await auth.logout();
  alert(JSON.stringify(res));
};
```

### 完整代码 <a id="full-code"></a>

```markup
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Authing SSO Example</title>
  </head>
  <body>
    <button id="btn-login">login</button>
    <button id="btn-track-session">trackSession</button>
    <button id="btn-logout">logout</button>
    <script src="https://cdn.jsdelivr.net/npm/@authing/sso/dist/AuthingSSO.umd.min.js"></script>
    <script>
      let auth = new AuthingSSO({
        appId: "YOUR_OIDC_APP_ID",
        appType: "oidc",
        appDomain: "YOUR_OIDC_APP_DOMAIN.authing.cn"
      });
      let login = document.getElementById("btn-login");
      let trackSession = document.getElementById("btn-track-session");
      let logout = document.getElementById("btn-logout");
      login.onclick = function() {
        auth.login();
      };
      trackSession.onclick = async function() {
        let res = await auth.trackSession();
        alert(JSON.stringify(res));
      };
      logout.onclick = async function() {
        let res = await auth.logout();
        alert(JSON.stringify(res));
      };
    </script>
  </body>
</html>
```

示例代码可从 [Github](https://github.com/Authing/authing-sso-demo) 上找到，建议将 Github 上的代码下载运行。

AuthingSSO 单点登录 SDK 完整参数列表：

{% page-ref page="../sdk/sdk-for-sso.md" %}

### **运行方法** <a id="run-the-demo"></a>

在终端中运行以下命令

```bash
$ git clone https://github.com/Authing/authing-sso-demo
$ cd authing-sso-demo
$ npm install -g http-server
$ http-server
```

之后在浏览器访问 [http://localhost:8080](http://localhost:8080)。

{% hint style="warning" %}
如果本地 8080 端口已被占用，应用可能会运行在 8081、8082 等后续端口。
{% endhint %}

### 运行效果 <a id="demo-result"></a>

首先点击 trackSession 按钮，获取到的登录状态为空，因为我们还未登录。

![](../.gitbook/assets/image%20%28184%29.png)

现在我们点击 login 按钮，会跳转到 OIDC 应用的用户认证页面，输入用户名密码进行登录。

![](../.gitbook/assets/image%20%28206%29.png)

浏览器被重定向到我们之前设置的回调链接。

点击 trackSession 按钮，此时能够获取到该用户的登录状态，包括用户 ID，应用 ID，应用类型，还有此用户的详细信息。

![](../.gitbook/assets/image%20%28470%29.png)

trackSession 返回数据格式如下：

```text
{
    "session": {
        "_id": "mbC_oeqTz0WPaspPAxpXEGXSDqlCo5i4",
        "__v": 0,
        "appId": "5cded9bf4efab36f02fa666a",
        "cookie": {
            "originalMaxAge": 86400000,
            "expires": "2020-04-04T14:55:22.397Z",
            "secure": true,
            "httpOnly": true,
            "path": "/cas",
            "sameSite": "none"
        },
        "type": "oidc",
        "userId": "5e71a7ec3e494a56f5f4d03b"
    },
    "userInfo": {
        "thirdPartyIdentity": {
            "updatedAt": "2020-03-17T01:34:40.998Z"
        },
        "email": "test3@123.com",
        "phone": "",
        "emailVerified": false,
        "phoneVerified": false,
        "username": "test3",
        "nickname": "",
        "company": "",
        "photo": "https://usercontents.authing.cn/authing-avatar.png",
        "browser": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36",
        "device": "",
        "loginsCount": 3,
        "registerMethod": "default:username-password",
        "blocked": false,
        "isDeleted": false,
        "oauth": "",
        "phoneCode": "",
        "name": "",
        "givenName": "",
        "familyName": "",
        "middleName": "",
        "profile": "",
        "preferredUsername": "",
        "website": "",
        "gender": "",
        "birthdate": "",
        "zoneinfo": "",
        "locale": "",
        "address": "",
        "formatted": "",
        "streetAddress": "",
        "locality": "",
        "region": "",
        "postalCode": "",
        "country": "",
        "updatedAt": "",
        "metadata": "",
        "_operate_history": [],
        "sendSMSCount": 0,
        "sendSMSLimitCount": 1000,
        "_id": "5e71a7ec3e494a56f5f4d03b",
        "registerInClient": "5cce4aa83ed9f97b4dfd95f0",
        "lastLogin": "2020-04-03T14:55:13.135Z",
        "signedUp": "2020-03-18T04:47:40.926Z",
        "__v": 0,
        "lastIP": "115.183.147.109",
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoidGVzdDNAMTIzLmNvbSIsImlkIjoiNWU3MWE3ZWMzZTQ5NGE1NmY1ZjRkMDNiIiwiY2xpZW50SWQiOiI1Y2NlNGFhODNlZDlmOTdiNGRmZDk1ZjAifSwiaWF0IjoxNTg1OTI1NzEzLCJleHAiOjE1ODcyMjE3MTN9.WUXSo4FIX7UlV4k2DbnPczwrAcbFvVz0cWmkSlt9d_s",
        "tokenExpiredAt": "2020-04-18T14:55:13.000Z",
        "__Token 验证方式说明": "https://docs.authing.cn/authing/advanced/authentication/verify-jwt-token#fa-song-token-gei-authing-fu-wu-qi-yan-zheng"
    },
    "urlParams": {
        "code": "N_J4aPRa6vIJUQyeO8NNJlozO4E",
        "__参数使用说明": {
            "OIDC 本地验证 access_token 和 id_token 的方式": "https://docs.authing.cn/authing/advanced/authentication/verify-jwt-token#oidc-secret-token",
            "OIDC access_token 和 id_token 合法性在线验证": "https://docs.authing.cn/authing/advanced/authentication/verify-jwt-token#yan-zheng-oidc-accesstoken-huo-idtoken-de-he-fa-xing",
            "OIDC code 使用文档": "https://docs.authing.cn/authing/advanced/oidc/oidc-authorization#shi-yong-code-huan-qu-token"
        },
        "__authing_hint": "code token id_token 字段只会在第一次回调到业务地址的时候从 url 取出，请自行存储以备使用"
    }
}
```

点击 logout 按钮，输出单点登出成功。

![](../.gitbook/assets/image%20%28296%29.png)

此时我们再点击 trackSession 按钮，可见登录状态为空，说明用户已经单点登出了。

![](../.gitbook/assets/image%20%28242%29.png)

## 访问用户个人中心页面 <a id="visit-profile"></a>

在 SSO 应用中，有独立的用户中心页面，用户可以修改自己的资料。

你的终端用户可以访问以下链接，进入个人资料修改页面：

```text
https://<appDomain>.authing.cn/login/profile
```

其中 `<appDomain>` 是你的 SSO 应用的二级域名。

如果用户未登录，会先要求用户登录再进入个人中心；对于已登录的用户则会直接进入个人中心。

![&#x4E2A;&#x4EBA;&#x4E2D;&#x5FC3;](../.gitbook/assets/image%20%28188%29.png)

## 检验 token 合法性 <a id="validate-token"></a>

成功登录之后，你获得的用户信息中包含一个 token 字段，这是用于的登录凭证，可以在后端用于判断用户身份。Authing 内部涉及到三种 token，建议先[了解它们的用途和区别](../authentication/oidc/oidc-params.md#idtokenaccesstoken-he-token-zhi-jian-de-qu-bie)，然后你可能需要了解如何验证它们的合法性。

{% page-ref page="../advanced/verify-jwt-token.md" %}

## 检验 token 的最佳实践 <a id="best-practise-to-validate-token"></a>

在调用 **trackSession** 函数之后，返回结果中 res.userInfo.token 字段是 Authing Token，只能通过向 Authing 服务器发送请求来检验合法性，不利于性能。可以将 Authing Token **转换为 OIDC Token**，然后在**本地验证 OIDC Token** 的合法性。

{% api-method method="post" host="https://<你的应用域名>.authing.cn" path="/oauth/oidc/token" %}
{% api-method-summary %}
Authing Token 转 OIDC Token
{% endapi-method-summary %}

{% api-method-description %}
使用用户的 **Authing Token** 换取 **OIDC token**。
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

Authing Token 换 OIDC Token 的代码示例：

```javascript
const qs = require('querystring');
const axios = require('axios');
let res = axios.post(
  'https://<你的 OIDC 应用域名>.authing.cn/oauth/oidc/token',
  qs.stringify({
    client_id: '你的 OIDC 应用 ID',
    client_secret: '你的 OIDC 应用 Secret',
    grant_type: 'authingToken',
    authingToken: '从 trackSession 中获取到的 Authing Token',
    scope: 'openid profile email address phone offline_access',
  }),
).then(res => {
  console.log(res.data)
/**
{
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJuQ0tSOEUwck1LR3RVdXprR0IzcUoiLCJzdWIiOiI1ZTg1YTY1YmMwNjA3YzBkMzAyZDdmMWIiLCJpc3MiOiJodHRwczovL3llbGV4aW4uaGVwLmF1dGhpbmcuY28vb2F1dGgvb2lkYyIsImlhdCI6MTU4NTg4NzQ2MiwiZXhwIjoxNTg1ODkxMDYyLCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIGVtYWlsIHBob25lIG9mZmxpbmVfYWNjZXNzIiwiYXVkIjoiNWU2NTA5YzhjMzNkZWUyN2FhMzZhNGUyIn0.t36WfP8NNFSGO7Ww7roYQ-Iz2ro4sgH8KZk20GzNKmw",
    "id_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1ZTg1YTY1YmMwNjA3YzBkMzAyZDdmMWIiLCJiaXJ0aGRhdGUiOiIiLCJmYW1pbHlfbmFtZSI6IiIsImdlbmRlciI6IiIsImdpdmVuX25hbWUiOiIiLCJsb2NhbGUiOiIiLCJtaWRkbGVfbmFtZSI6IiIsIm5hbWUiOiIiLCJuaWNrbmFtZSI6IiIsInBpY3R1cmUiOiJodHRwczovL3VzZXJjb250ZW50cy5hdXRoaW5nLmNuL2F1dGhpbmctYXZhdGFyLnBuZyIsInByZWZlcnJlZF91c2VybmFtZSI6IiIsInByb2ZpbGUiOiIiLCJ1cGRhdGVkX2F0IjoiIiwid2Vic2l0ZSI6IiIsInpvbmVpbmZvIjoiIiwidXNlcm5hbWUiOiJlcmhhZXJnIiwiY29tcGFueSI6IiIsImJyb3dzZXIiOiJNb3ppbGxhLzUuMCAoTWFjaW50b3NoOyBJbnRlbCBNYWMgT1MgWCAxMF8xNF82KSBBcHBsZVdlYktpdC81MzcuMzYgKEtIVE1MLCBsaWtlIEdlY2tvKSBDaHJvbWUvODAuMC4zOTg3LjE0OSBTYWZhcmkvNTM3LjM2IiwiZGV2aWNlIjoiIiwibG9naW5zX2NvdW50IjoxLCJyZWdpc3Rlcl9tZXRob2QiOiJkZWZhdWx0OnVzZXJuYW1lLXBhc3N3b3JkIiwiYmxvY2tlZCI6ZmFsc2UsImxhc3RfaXAiOiIxMjQuMjA0LjU2Ljk4IiwicmVnaXN0ZXJfaW5fdXNlcnBvb2wiOiI1ZTY1MDljOGMzM2RlZTI0YzkzNmE0ZGIiLCJsYXN0X2xvZ2luIjoiMjAyMC0wNC0wMlQwODo0NjozMi43NTVaIiwic2lnbmVkX3VwIjoiMjAyMC0wNC0wMlQwODo0NjoxOS42MTlaIiwiZW1haWwiOiJhZXJhZXJAMTIzLmNvbSIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwicGhvbmVfbnVtYmVyIjoiIiwicGhvbmVfbnVtYmVyX3ZlcmlmaWVkIjpmYWxzZSwiS0VZIjoiVkFMVUUiLCJhdWQiOiI1ZTY1MDljOGMzM2RlZTI3YWEzNmE0ZTIiLCJleHAiOjE1ODU4OTEwNjIsImlhdCI6MTU4NTg4NzQ2MiwiaXNzIjoiaHR0cHM6Ly95ZWxleGluLmhlcC5hdXRoaW5nLmNvL29hdXRoL29pZGMifQ.USFlIFkL23jlehnyjNlEOaLUYCqN_jNY8bFYiAiuRhg",
    "refresh_token": "3tjpVQqQa1uMT55U0sonRtkCV6U",
    "expires_in": 3600,
    "token_type": "Bearer",
    "scope": "openid profile email phone offline_access"
}
*/
}).catch(err => {
  console.log(err.response.data)
});

```

然后可以通过 OIDC 应用的 secret 来检验 id\_token 的合法性：

```javascript
const jwt = require('jsonwebtoken');
const id_token = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1ZTg1YTY1YmMwNjA3YzBkMzAyZDdmMWIiLCJiaXJ0aGRhdGUiOiIiLCJmYW1pbHlfbmFtZSI6IiIsImdlbmRlciI6IiIsImdpdmVuX25hbWUiOiIiLCJsb2NhbGUiOiIiLCJtaWRkbGVfbmFtZSI6IiIsIm5hbWUiOiIiLCJuaWNrbmFtZSI6IiIsInBpY3R1cmUiOiJodHRwczovL3VzZXJjb250ZW50cy5hdXRoaW5nLmNuL2F1dGhpbmctYXZhdGFyLnBuZyIsInByZWZlcnJlZF91c2VybmFtZSI6IiIsInByb2ZpbGUiOiIiLCJ1cGRhdGVkX2F0IjoiIiwid2Vic2l0ZSI6IiIsInpvbmVpbmZvIjoiIiwidXNlcm5hbWUiOiJlcmhhZXJnIiwiY29tcGFueSI6IiIsImJyb3dzZXIiOiJNb3ppbGxhLzUuMCAoTWFjaW50b3NoOyBJbnRlbCBNYWMgT1MgWCAxMF8xNF82KSBBcHBsZVdlYktpdC81MzcuMzYgKEtIVE1MLCBsaWtlIEdlY2tvKSBDaHJvbWUvODAuMC4zOTg3LjE0OSBTYWZhcmkvNTM3LjM2IiwiZGV2aWNlIjoiIiwibG9naW5zX2NvdW50IjoxLCJyZWdpc3Rlcl9tZXRob2QiOiJkZWZhdWx0OnVzZXJuYW1lLXBhc3N3b3JkIiwiYmxvY2tlZCI6ZmFsc2UsImxhc3RfaXAiOiIxMjQuMjA0LjU2Ljk4IiwicmVnaXN0ZXJfaW5fdXNlcnBvb2wiOiI1ZTY1MDljOGMzM2RlZTI0YzkzNmE0ZGIiLCJsYXN0X2xvZ2luIjoiMjAyMC0wNC0wMlQwODo0NjozMi43NTVaIiwic2lnbmVkX3VwIjoiMjAyMC0wNC0wMlQwODo0NjoxOS42MTlaIiwiZW1haWwiOiJhZXJhZXJAMTIzLmNvbSIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwicGhvbmVfbnVtYmVyIjoiIiwicGhvbmVfbnVtYmVyX3ZlcmlmaWVkIjpmYWxzZSwiS0VZIjoiVkFMVUUiLCJhdWQiOiI1ZTY1MDljOGMzM2RlZTI3YWEzNmE0ZTIiLCJleHAiOjE1ODU4OTEwNjIsImlhdCI6MTU4NTg4NzQ2MiwiaXNzIjoiaHR0cHM6Ly95ZWxleGluLmhlcC5hdXRoaW5nLmNvL29hdXRoL29pZGMifQ.USFlIFkL23jlehnyjNlEOaLUYCqN_jNY8bFYiAiuRhg';
const secret = '你的 OIDC 应用 Secret';
let decoded;
try {
  decoded = jwt.verify(id_token, secret);
} catch (err) {
  console.log('id_token 非法');
} 
```

## 接下来你可能还需要 <a id="next"></a>

学习 Authing 的基础知识以及我们会用到的专业术语：

{% page-ref page="basic.md" %}

控制台是你管理所有 Authing 资源的地方，了解 Authing 控制台各模块包含的内容和你可以在控制台中做的事情：

{% page-ref page="dashboard.md" %}

了解 Authing 提供的多种部署模型，以帮助你选择该以怎样的形式部署 Authing：

{% page-ref page="deployment.md" %}

了解 「SDK for 单点登录」的详细参数：

{% page-ref page="../sdk/sdk-for-sso.md" %}



