---
description: >-
  单点登录（Single Sign On），简称为SSO，是目前比较流行的企业业务整合的解决方案之一。
  SSO的定义是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。
---

# 实现单点登录

## 开始之前

如果你不了解用户池、单点登录和认证授权，建议先阅读[基础概念](https://docs.authing.cn/authing/quickstart/basic)。

### 预备知识

1. 基本的 HTML 和 CSS 知识
2. 中级 JavaScript 技能

### 所需工具

1. 你喜欢的文本编辑器
2. 可以在本地运行的 Web 服务器（比如：`npm install http-server -g`）

## 注册一个 Authing 账号

如果你还没有账号，请[点击这里注册 Authing 账号](https://authing.cn/login)，注册完成后请[进入控制台](https://authing.cn/dashboard)并创建一个用户池。

![&#x521B;&#x5EFA;&#x7528;&#x6237;&#x6C60;](../.gitbook/assets/image%20%2863%29.png)

![&#x9009;&#x62E9;&#x5E94;&#x7528;&#x7C7B;&#x578B;](../.gitbook/assets/image%20%2849%29.png)

## 创建一个 OIDC 应用

**第三方登录** -&gt; **OIDC 应用**选项卡，点击蓝色的「创建 OIDC 应用」按钮。

![&#x521B;&#x5EFA; ODC &#x5E94;&#x7528;](../.gitbook/assets/image%20%2841%29.png)

填上你的应用名，指定此 OIDC 应用的二级域名（认证地址），回调地址，其他参数保留默认即可。点击「确定」。 

![&#x914D;&#x7F6E; OIDC &#x5E94;&#x7528;](../.gitbook/assets/image%20%28160%29.png)

**参数解释**

**认证地址**，一个 authing.cn 的二级域名，用户将在此网址进行登录。

**回调 URL**，OIDC 登录成功后，回调到开发者自己业务的地址。本教程为演示，填写的地址是 http://localhost:8080，实际场景下要填写自己的业务地址。

在应用列表中点击刚创建好的应用，记录下 AppID，二级域名，供以后使用。

## 使用 AuthingSSO SDK 集成单点登录

### 创建一个空白的 HTML 文档用来编写 Authing 程序

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

### 添加三个按钮

增加三个按钮控件到 body 中，目的是为了演示如何使用 SDK 管理单点登录状态。

```markup
<button id="btn-login">login</button>
<button id="btn-track-session">trackSession</button>
<button id="btn-logout">logout</button>
```

### 引入 AuthingSSO 并初始化

从 CDN 加载 [AuthingSSO](https://github.com/Authing/AuthingSSO) 的 SDK。填入你的 OIDC 应用 ID 和 域名，进行初始化。

```markup
<script src="https://cdn.jsdelivr.net/npm/@authing/sso/dist/SSO.umd.min.js"></script>
<script>
  let auth = new SSO({
    appId: "YOUR_OIDC_APP_ID",
    appType: "oidc",
    appDomain: "OIDC_APP_DOMAIN.authing.cn"
  });
</script>
```

### 为按钮注册点击事件

达到的效果是：

* 点击 login 按钮，浏览器会跳转到 OIDC 登录页面，与用户完成身份确认。
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

### 完整代码

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
    <script src="https://cdn.jsdelivr.net/npm/@authing/sso/dist/SSO.umd.min.js"></script>
    <script>
      let auth = new SSO({
        appId: "YOUR_OIDC_APP_ID",
        appType: "oidc",
        appDomain: "OIDC_APP_DOMAIN.authing.cn"
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

### **运行方法**

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

### 运行效果

最初，我们没有登录，因此，点击 trackSession 按钮获取到的登录状态为空。

![](../.gitbook/assets/image%20%28187%29.png)

现在我们点击 login 按钮，会跳转到 OIDC 应用的用户认证页面，输入用户名密码进行登录。

![](../.gitbook/assets/image%20%28119%29.png)

浏览器被重定向到我们之前设置的回调链接，记下 code 参数，用于后面换取用户信息。

![](../.gitbook/assets/image%20%28261%29.png)

现在我们回到刚刚编写的 html 页面，点击 trackSession 按钮，此时能够获取到该用户的登录状态，包括用户 ID，应用 ID，应用类型。

![](../.gitbook/assets/image%20%28234%29.png)

点击 logout 按钮，输出单点登出成功。

![](../.gitbook/assets/image%20%2864%29.png)

此时我们再点击 trackSession 按钮，可见登录状态为空，说明用户已经单点登出了。

![](../.gitbook/assets/image%20%28277%29.png)

### 获取用户信息

#### 使用 OIDC 流程中返回的 code 换取 access\_token

向以下地址发送 POST 请求：

```text
POST https://OIDC_APP_DOMAIN.authing.cn/oauth/oidc/token
```

body 参数

| 参数名 | 意义 |
| :--- | :--- |
| client\_id | OIDC 应用的 app\_id |
| redirect\_uri | 在控制台配置的 OIDC 回调 url 其中的一个值 |
| scope | 需要请求的权限，如果需要获取 email 和手机号需要有 phone email，如果需要 refresh\_token 需要包含 offline\_access [参考 scope 表格](http://docs.authing.cn/authing/advanced/oidc/oidc-params#scope-can-shu-dui-ying-de-yong-hu-xin-xi) |
| response\_type | OIDC 模式，可以为 code, id\_token, id\_token token, code id\_token, code token, code id\_token token [参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationExamples) |
| prompt | 可以为 none，login，consent 或 select\_account，指定 AP 与 End-User 的交互方式，如需 refresh\_token，必须为 consent [参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) |
| state | 一个随机字符串，用于防范 CSRF 攻击，如果 response 中的 state 值和发送请求之前设置的 state 值不同，说明受到攻击 |
| nonce | 一个随机字符串，用于防范 Replay 攻击 |

返回示例

```javascript
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJqdGkiOiJ4R01uczd5cmNFckxiakNRVW9US1MiLCJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJpc3MiOiJodHRwczovL2F1dGhpbmcuY24iLCJpYXQiOjE1NTQ1Mzc4NjksImV4cCI6MTU1NDU0MTQ2OSwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBvZmZsaW5lX2FjY2VzcyBwaG9uZSBlbWFpbCIsImF1ZCI6IjVjYTc2NWUzOTMxOTRkNTg5MWRiMTkyNyJ9.wX05OAgYuXeYM7zCxhrkvTO_taqxrCTG_L2ImDmQjMml6E3GXjYA9EFK0NfWquUI2mdSMAqohX-ndffN0fa5cChdcMJEm3XS9tt6-_zzhoOojK-q9MHF7huZg4O1587xhSofxs-KS7BeYxEHKn_10tAkjEIo9QtYUE7zD7JXwGUsvfMMjOqEVW6KuY3ZOmIq_ncKlB4jvbdrduxy1pbky_kvzHWlE9El_N5qveQXyuvNZVMSIEpw8_y5iSxPxKfrVwGY7hBaF40Oph-d2PO7AzKvxEVMamzLvMGBMaRAP_WttBPAUSqTU5uMXwMafryhGdIcQVsDPcGNgMX6E1jzLA",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InIxTGtiQm8zOTI1UmIyWkZGckt5VTNNVmV4OVQyODE3S3gwdmJpNmlfS2MifQ.eyJzdWIiOiI1YzlmNzVjN2NjZjg3YjA1YTkyMWU5YjAiLCJub25jZSI6IjIyMTIxIiwiYXRfaGFzaCI6Ik5kbW9iZVBZOEFFaWQ2T216MzIyOXciLCJzaWQiOiI1ODM2NzllNC1lYWM5LTRjNDEtOGQxMS1jZWFkMmE5OWQzZWIiLCJhdWQiOiI1Y2E3NjVlMzkzMTk0ZDU4OTFkYjE5MjciLCJleHAiOjE1NTQ1NDE0NjksImlhdCI6MTU1NDUzNzg2OSwiaXNzIjoiaHR0cHM6Ly9hdXRoaW5nLmNuIn0.IQi5FRHO756e_eAmdAs3OnFMU7QuP-XtrbwCZC1gJntevYJTltEg1CLkG7eVhdi_g5MJV1c0pNZ_xHmwS0R-E4lAXcc1QveYKptnMroKpBWs5mXwoOiqbrjKEmLMaPgRzCOdLiSdoZuQNw_z-gVhFiMNxI055TyFJdXTNtExt1O3KmwqanPNUi6XyW43bUl29v_kAvKgiOB28f3I0fB4EsiZjxp1uxHQBaDeBMSPaRVWQJcIjAJ9JLgkaDt1j7HZ2a1daWZ4HPzifDuDfi6_Ob1ZL40tWEC7xdxHlCEWJ4pUIsDjvScdQsez9aV_xMwumw3X4tgUIxFOCNVEvr73Fg",
  "refresh_token": "WPsGJbvpBjqXz6IJIr1UHKyrdVF",
  "scope": "openid profile offline_access phone email",
  "token_type": "Bearer"
}
```

#### 验证 access\_token 和 id\_token 的合法性

OIDC 默认使用 OIDC 应用的 secret 对 token 进行验证（也就是在创建应用时默认选择 HS256 算法）。 如果你使用 javascript 那么可以使用 jsonwebtoken 进行验证：

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

#### 使用 access\_token 换取用户信息

开发者在自己的服务中可以使用 access\_token 换取用户信息。根据 scope 的不同，这里的返回信息也会有所不同，字段符合 [OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationExamples)，字段解释请参考[用户信息字段含义](https://docs.authing.cn/authing/advanced/oidc/oidc-params#yong-hu-xin-xi-zi-duan-han-yi)。 请求链接：

```text
GET https://users.authing.cn/oauth/oidc/user/userinfo?access_token=<access_token>
```

返回示例：

```javascript
{
  "sub": "<用户在 Authing 的唯一标识>",
  "nickname": "Authing",
  "name": "张三",
  "locale": "en-US"
}
```

更多字段解释请参考[用户信息字段含义](https://docs.authing.cn/authing/advanced/oidc/oidc-params#yong-hu-xin-xi-zi-duan-han-yi)。

## 接下来你可能还需要

了解 OIDC 协议：

{% page-ref page="../advanced/oidc/li-jie-oidc-liu-cheng.md" %}

控制台是你管理所有 Authing 资源的地方，了解 Authing 控制台各模块包含的内容和你可以在控制台中做的事情：

{% page-ref page="dashboard.md" %}

了解 Authing 提供的多种部署模型，以帮助你选择该以怎样的形式部署 Authing：

{% page-ref page="deployment.md" %}



