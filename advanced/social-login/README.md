---
description: 使用微信、Github 等社会化登录。
---

# 接入社会化登录

{% hint style="info" %}
如果你使用了 Authing 的二级域名处理用户认证，请参考：[在云上配置社会化登录](https://docs.authing.cn/authing/advanced/social-login/config-social-login-in-cloud)。
{% endhint %}

社会化登录指使用微信、Github 等互联网服务登录自己的服务，Authing 目前支持 **Github 登录、微信 PC 网页版扫码登录、微信移动网页登录、小程序登录、支付宝登录、钉钉登录、QQ 登录和微博登录**。

{% hint style="info" %}
本章节将以接入 Github 为例，演示如何使用 Authing 快速接入社会化登录，其他社会化登录同理。
{% endhint %}

线上演示：

{% embed url="https://sample.authing.cn/\#/" %}

## 创建 Github OAuth 应用

请点击下面这个卡片，按照官方的说明流程创建一个 Github OAuth 应用：

{% embed url="https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/" %}

{% hint style="info" %}
**重要提示，在创建 Github OAuth 应用时填写的 Callback URI 请填写为：**

\*\*\*\*[https://oauth.authing.cn/oauth/github/redirect](https://oauth.authing.cn/oauth/github/redirect)

如下图所示：

![](../../.gitbook/assets/image%20%28205%29.png)
{% endhint %}

## 在 Authing 控制台配置 Github OAuth 信息

在 Github 创建完 OAuth 应用后请按照下图所示在 Authing 控制台中配置 Github OAuth 信息：

![](../../.gitbook/assets/image%20%28106%29.png)

打开开关后将弹出如下信息框：

![](../../.gitbook/assets/image%20%2888%29.png)

相关参数解释如下：

| Client ID | Client Secret | Redirect |
| :--- | :--- | :--- |
| 在 OAuth 平台上申请的 APP ID 或 Client ID | OAuth 平台提供的 Client Secret 或 App Secret | 成功或失败后的回调 URL（你的业务地址）。如果你需要在 OIDC 或 OAuth 应用中配置社会化登录，此处地址可填写为任意值，如：\#。 |

![](../../.gitbook/assets/image%20%28267%29.png)

配置完成后请点击「确定」保存信息。

接下来，你可以通过拼接以下 URL 执行 OAuth 登录流程：

[https://github.com/login/oauth/authorize/?client\_id=&lt;Github\_Client\_ID&gt;&state=&lt;Authing\_Client\_ID&gt;](https://github.com/login/oauth/authorize/?client_id=<Github_Client_ID>&state=<Authing_ClientId>
)

如 Authing 的官方「使用 Github 登录」链接：

[https://github.com/login/oauth/authorize/?client\_id=5446d8d6083f27fed5b4&state=59f86b4832eb28071bdd9214](https://github.com/login/oauth/authorize/?client_id=5446d8d6083f27fed5b4&state=59f86b4832eb28071bdd9214)

{% hint style="info" %}
你可以在 Web 页面上放置一个可点击的按钮或 Logo 链接到上面的链接以便用户可以点击登录。
{% endhint %}

## 获取用户数据

用户在访问上面拼接好的链接后，将回调至开发者配置好的 Redirect URL，并附带以下参数：

| 参数 | 说明 |
| :--- | :--- |
| code | 错误或成功代码，200 为成功，非 200 为失败 |
| message | 成功或错误信息 |
| data | userInfo，若 code 为非 200 不返回此参数 |
| bindOAuth | 是否是登录操作， 无此参数或此参数为`0`则为登录, 此参数为`1`则为绑定账号操作, 此时可使用`data`参数中的数据[绑定OAuth](../../sdk/sdk-for-node/bind-social-login.md#bang-ding-she-hui-hua-zhang-hao) |

以下是使用 JavaScript 从 URL 参数中获取用户数据的代码：

```javascript
// 获取 URL 参数
function getQueryString(name) {
    var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
    var r = window.location.search.substr(1).match(reg);
    if (r != null) {
        return unescape(r[2]);
    }
    return null;
}

// 将 Code 转为 Int 类型，方便判断
const code = parseInt(getQueryString('code'));

if(code !== 200) {
  // 出错了
  const errorMsg = getQueryString('message');
  // 展示 errorMsg 给用户或执行其他业务 ...
  
}else {
  const userInfo = getQueryString('data');
  
  // 将 token 存储到 localStorage 
  // 建议在之后的请求中附带 Token，并由后端验证 Token 合法性
  localStorage.setItem('token', userInfo.token);
}
```

{% hint style="info" %}
取得用户数据后，若你想验证 Token 合法性，请参考：[验证 Token 合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。
{% endhint %}

### userInfo 示例

```javascript
{
    "_id": "5b88aaea349e2d0001a5b718",
    "email": "test@test.com",
    "emailVerified": false,
    "unionid": "123456",
    "oauth": "{\"login\":\"123456\",\"id\":123456,\"node_id\":\"MDQ6VXNlcjI0Njk2ODg=\",\"avatar_url\":\"https://avatars3.githubusercontent.com/u/2469688?v=4\",\"gravatar_id\":\"\",\"url\":\"https://api.github.com/users/test\",\"html_url\":\"https://github.com/test\",\"followers_url\":\"https://api.github.com/users/test/followers\",\"following_url\":\"https://api.github.com/users/test/following{/other_user}\",\"gists_url\":\"https://api.github.com/users/test/gists{/gist_id}\",\"starred_url\":\"https://api.github.com/users/test/starred{/owner}{/repo}\",\"subscriptions_url\":\"https://api.github.com/users/test/subscriptions\",\"organizations_url\":\"https://api.github.com/users/test/orgs\",\"repos_url\":\"https://api.github.com/users/test/repos\",\"events_url\":\"https://api.github.com/users/test/events{/privacy}\",\"received_events_url\":\"https://api.github.com/users/test/received_events\",\"type\":\"User\",\"site_admin\":false,\"name\":\"test\",\"company\":\"test\",\"blog\":\"http://test.com\",\"location\":\"Beijing, China\",\"email\":\"test@test.com\",\"hireable\":null,\"bio\":\"Being NO.1\",\"public_repos\":91,\"public_gists\":0,\"followers\":109,\"following\":27,\"created_at\":\"2012-10-02T06:38:50Z\",\"updated_at\":\"2018-07-23T05:51:23Z\"}",
    "registerMethod": "oauth:github",
    "username": "test",
    "nickname": "",
    "company": "",
    "photo": "https://avatars3.githubusercontent.com/u/2469688?v=4",
    "browser": "",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoieGlleWFuZ0Bkb2RvcmEuY24iLCJ1bmlvbmlkIjoiMjQ2OTY4OCIsImlkIjoiNWI4OGFhZWEzNDllMmQwMDAxYTViNzE4IiwiY2xpZW50SWQiOiI1YTlmYTI2Y2Y4NjM1YTAwMDE4NTUyOGMifSwiaWF0IjoxNTU4MTAwMDczLCJleHAiOjE1NTkzOTYwNzN9.7R_-CGnbPBRjHFaVS0ERWMaGfR_24zYJiBTJvJ4XYxk",
    "tokenExpiredAt": "Sat Jun 01 2019 21:34:33 GMT+0800 (CST)",
    "loginsCount": 47,
    "lastLogin": "Fri May 17 2019 21:34:33 GMT+0800 (CST)",
    "lastIP": "52.231.14.216",
    "signedUp": "Fri Aug 31 2018 10:41:46 GMT+0800 (CST)",
    "blocked": false,
    "isDeleted": false
}
```

## 完成接入

恭喜你，此时已经完成了 Github OAuth 登录。

## Authing 支持的社会化登录

下表列举了 Authing 支持的社会化登录及其配置信息。

| 社会化登录名称 | Callback URL | 注册地址 |
| :--- | :--- | :--- |
| 微信 PC 网页版 | [https://oauth.authing.cn/oauth/wechat/redirect](https://oauth.authing.cn/oauth/wechat/redirect) | [微信开放平台](https://open.weixin.qq.com/cgi-bin/index?t=home/index&lang=zh_CN) |
| Github | [https://oauth.authing.cn/oauth/github/redirect](https://oauth.authing.cn/oauth/github/redirect) | [Creating a github APP](https://developer.github.com/apps/building-github-apps/creating-a-github-app/) |
| QQ | [https://oauth.authing.cn/oauth/qq/redirect](https://oauth.authing.cn/oauth/qq/redirect) | [QQ 互联](https://connect.qq.com/manage.html#/) |
| 新浪微博 | [https://oauth.authing.cn/oauth/weibo/redirect](https://oauth.authing.cn/oauth/weibo/redirect) | [新浪微博开放平台](https://open.weibo.com/developers) |
| 钉钉 | [https://oauth.authing.cn/oauth/dingtalk/redirect](https://oauth.authing.cn/oauth/dingtalk/redirect) | [钉钉开发者平台](https://open-dev.dingtalk.com/#/loginMan) |

Callback URL 指在创建微信 OAuth 应用、Github OAuth 等应用时在其平台中填写的 Redirect URL （有的平台可能是 Callback URL）。

以 Github 为例，在创建 Github OAuth 应用时，创建表单中的 Callback URL 应该填写为：

| [https://oauth.authing.cn/oauth/github/redirect](https://oauth.authing.cn/oauth/github/redirect) |
| :--- |


![](../../.gitbook/assets/image%20%28205%29.png)

而在 Authing 控制台中配置的 Redirect URL 配置为你自己的业务地址：

![](../../.gitbook/assets/image%20%28172%29.png)

