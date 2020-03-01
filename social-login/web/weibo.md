# 新浪微博登录

## 准备工作 <a id="prepare"></a>

你一共需要准备以下内容：

1. [注册 Authing 开发者账号](../../quickstart/create-authing-account.md)
2. 申请一个微博网页应用
3. 在 Authing 控制台填入微博网页应用信息

### 申请微博网页应用

前往[微博开放平台](https://open.weibo.com/connect)按照官方指引注册一个网页应用，并将回调链接设置为：[https://oauth.authing.cn/oauth/weibo/redirect](https://oauth.authing.cn/oauth/weibo/redirect) 。

### 在 Authing 控制台填入微博网页应用信息

创建完微博网页应用后请按照下图指示在 Authing 控制台找到微博社会化登录配置：

![](../../.gitbook/assets/wei-bo.png)

在此需要填入你的微博应用信息：

* Client ID
* Client Secret
* Redirect：**这是你的业务回调域名，和微博网页应用里配置的回调链接不一样**。比如你的网站域名是 https://example.com ， 处理 Authing 回调请求的 url 为 /auth/weibo/callback ， 那么你应该填写为 `https://example.com/auth/weibo/callback`。如果你需要在 OIDC 或 OAuth 应用中单独配置回调链接，此处地址可填入： \#。
* Scopes（可选）: 默认情况下，Authing 只会向用户申请基础用户信息（如头像、昵称等）的授权，如果你需要更多高级权限，可以勾选上对应的选项。详细说明请见[配置登录授权 Scopes](social-login-scopes.md#sina-blog)。

![](../../.gitbook/assets/image%20%28417%29.png)

配置完成后请点击「确定」保存信息。

## 开始接入

### 引导用户跳转到微博授权页面

你可以调用 Authing 提供的接口，生成微博授权页面链接。

{% api-method method="get" host="https://oauth.authing.cn" path="/oauth/weibo/url/:userPoolId" %}
{% api-method-summary %}

{% endapi-method-summary %}

{% api-method-description %}
生成微博授权链接。
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
    "data": "微博授权链接",
    "code": 200
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
之后你可以在 Web 页面上放置一个可点击的按钮或 Logo 链接到上面的链接以便用户可以点击登录。
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

恭喜你，此时已经接入了微博网页登录。获取到用户信息之后，你可以得到登录凭证 token，你可以在后续的 API 请求中携带上此 token, 然后在后端接口中根据此 token 区分不同用户，详情请见[验证 token](../../advanced/verify-jwt-token.md#yan-zheng-authing-qian-fa-de-token)。
