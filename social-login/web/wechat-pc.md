# 微信 PC 扫码登录

## 准备工作 <a id="prepare"></a>

你一共需要准备以下内容：

1. [注册 Authing 开发者账号](../../quickstart/create-authing-account.md)
2. 申请一个微信网页应用
3. 在 Authing 控制台填入微信网页应用信息

### 申请微信网页应用

按照[微信官方文档](https://open.weixin.qq.com/cgi-bin/frame?t=home/web_tmpl&lang=zh_CN)指引注册成功之后，你可以看到你的微信网页应用的 Client ID（App ID） 和 Client Secret（App Secret），并请确保该应用获取到了微信登录的权限。

![](../../.gitbook/assets/image%20%28165%29.png)

{% hint style="info" %}
请将授权回调域设置为 oauth.authing.cn
{% endhint %}

![](../../.gitbook/assets/image%20%2833%29.png)

### 在 Authing 控制台填入微信网页应用信息

在微信开放平台创建完微信网页应用后请按照下图指示在 Authing 控制台找到微信电脑网页版配置：

![](../../.gitbook/assets/wei-xin-pc.png)

在此需要填入你的微信网页应用信息：

* Client ID: 微信网页应用的 App ID
* Client Secret: 微信网页应用的 App Secret
* Redirect：**这是你的业务回调域名，非 Authing 的链接**。比如你的网站域名是 https://example.com ， 处理 Authing 回调请求的 url 为 /auth/wechat/callback ， 那么你应该填写为 `https://example.com/auth/wechat/callback`。如果你需要在 OIDC 或 OAuth 应用中单独配置回调链接，此处地址可填入： \#。

![](../../.gitbook/assets/image%20%28227%29.png)

## 开始接入

### 生成微信授权链接

你可以调用 Authing 提供的接口，生成供用户跳转到微信扫码登录页面的链接。

{% api-method method="get" host="https://oauth.authing.cn" path="/oauth/wechatpc/url/:userPoolId" %}
{% api-method-summary %}

{% endapi-method-summary %}

{% api-method-description %}
生成微信授权链接。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="userPoolId" type="string" required=true %}
你的用户池 ID
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    "data": "https://open.weixin.qq.com/connect/qrconnect?appid=wxb127de9815c47251&redirect_uri=https%3A%2F%2Foauth.authing.cn%2Foauth%2Fwechat%2Fredirect&response_type=code&scope=snsapi_login&state=59f86b4832eb28071bdd9214-wechatpc#wechat_redirect",
    "code": 200
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

示例：Authing 官方用户池的请求生成微信授权链接的 URL 为：[https://oauth.authing.cn/oauth/wechatpc/url/59f86b4832eb28071bdd9214](https://oauth.authing.cn/oauth/wechatpc/url/59f86b4832eb28071bdd9214)

会得到以下响应数据：

```javascript
{
    "data": "https://open.weixin.qq.com/connect/qrconnect?appid=wxb127de9815c47251&redirect_uri=https%3A%2F%2Foauth.authing.cn%2Foauth%2Fwechat%2Fredirect&response_type=code&scope=snsapi_login&state=59f86b4832eb28071bdd9214-wechatpc#wechat_redirect",
    "code": 200
}
```

那么此次请求生成的微信授权链接为：[https://open.weixin.qq.com/connect/qrconnect?appid=wxb127de9815c47251&redirect\_uri=https%3A%2F%2Foauth.authing.cn%2Foauth%2Fwechat%2Fredirect&response\_type=code&scope=snsapi\_login&state=59f86b4832eb28071bdd9214-wechatpc\#wechat\_redirect](https://open.weixin.qq.com/connect/qrconnect?appid=wxb127de9815c47251&redirect_uri=https%3A%2F%2Foauth.authing.cn%2Foauth%2Fwechat%2Fredirect&response_type=code&scope=snsapi_login&state=59f86b4832eb28071bdd9214-wechatpc#wechat_redirect)

访问此页面你应该可以看到类似以下页面：

![](../../.gitbook/assets/image%20%28454%29.png)

{% hint style="info" %}
你可以在 Web 页面上放置一个可点击的按钮或 Logo 链接到上面的链接以便用户可以点击登录。
{% endhint %}

### 处理 Authing 回调请求

上一步用户同意授权之后，会先跳转到 Authing 服务器，之后 Authing 会携带用户信息跳转到开发者在 Authing 控制台中配置的业务回调链接，并附带以下 Get 请求参数：

| 参数 | 说明 |
| :--- | :--- |
| code | 错误或成功代码，200 为成功，非 200 为失败 |
| message | 成功或错误信息 |
| data | userInfo，若 code 为非 200 不返回此参数 |
| bindOAuth | 是否是登录操作， 无此参数或此参数为`0`则为登录, 此参数为`1`则为绑定账号操作, 此时可使用`data`参数中的数据[绑定OAuth](../../sdk/sdk-for-node/bind-social-login.md#bang-ding-she-hui-hua-zhang-hao) |

data 数据示例：

```javascript
{
    "_id": "5b88aaea349e2d0001a5b718",
    "email": "test@test.com",
    "emailVerified": false,
    "unionid": "123456",
    "oauth": "{\"login\":\"123456\",\"id\":123456,\"node_id\":\"MDQ6VXNlcjI0Njk2ODg=\",\"avatar_url\":\"https://avatars3.githubusercontent.com/u/2469688?v=4\",\"gravatar_id\":\"\",\"url\":\"https://api.github.com/users/test\",\"html_url\":\"https://github.com/test\",\"followers_url\":\"https://api.github.com/users/test/followers\",\"following_url\":\"https://api.github.com/users/test/following{/other_user}\",\"gists_url\":\"https://api.github.com/users/test/gists{/gist_id}\",\"starred_url\":\"https://api.github.com/users/test/starred{/owner}{/repo}\",\"subscriptions_url\":\"https://api.github.com/users/test/subscriptions\",\"organizations_url\":\"https://api.github.com/users/test/orgs\",\"repos_url\":\"https://api.github.com/users/test/repos\",\"events_url\":\"https://api.github.com/users/test/events{/privacy}\",\"received_events_url\":\"https://api.github.com/users/test/received_events\",\"type\":\"User\",\"site_admin\":false,\"name\":\"test\",\"company\":\"test\",\"blog\":\"http://test.com\",\"location\":\"Beijing, China\",\"email\":\"test@test.com\",\"hireable\":null,\"bio\":\"Being NO.1\",\"public_repos\":91,\"public_gists\":0,\"followers\":109,\"following\":27,\"created_at\":\"2012-10-02T06:38:50Z\",\"updated_at\":\"2018-07-23T05:51:23Z\"}",
    "registerMethod": "oauth:wechat-pc",
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

## 完成接入

恭喜你，此时已经接入了微信 PC 扫码登录。获取到用户信息之后，你可以得到登录凭证 token，你可以在后续的 API 请求中携带上此 token, 然后在后端接口中根据此 token 区分不同用户，详情请见[验证 token](../../advanced/verify-jwt-token.md#yan-zheng-authing-qian-fa-de-token)。

