# 微信 APP 内网页登录



{% hint style="success" %}
微信 APP 内网页登录提供在线体验地址：[https://sample.authing.cn/wxmobile/](https://sample.authing.cn/wxmobile/) （请在微信客户端内打开哦）。以及 GitHub Demo 源码：[https://github.com/Authing/authing-wxmp-sdk](https://github.com/Authing/authing-wxmp-sdk)
{% endhint %}

## 准备工作

你一共需要准备以下内容：

1. [注册 Authing 开发者账号](../../quickstart/create-authing-account.md)
2. 注册一个微信**服务号**并通过**微信认证**。
3. 在 Authing 控制台填入微信服务号信息

### 注册微信服务号

按照[微信官方文档](https://mp.weixin.qq.com/cgi-bin/readtemplate?t=register/step1_tmpl&lang=zh_CN&token=)指引注册成功之后，你可以在微信公众平台后台的 **开发** -&gt; **基本配置** 页面获取开发者ID\(AppID\) 和开发者密码\(AppSecret\)。

![](../../.gitbook/assets/image%20%28526%29.png)

之后需在微信公众平台后台的 **设置** -&gt; **公众号设置** -&gt; **功能设置** 页面设置 **网页授权域名** 为 **oauth.authing.cn：**

![](../../.gitbook/assets/image%20%28529%29.png)

出于安全验证考虑，微信服务器需要和 Authing 服务器做一次请求验证，开发者需要下载 txt 文件，并记录 **文件名** 和 **文本内容，**之后需要填入 Authing 控制台**。**

![](../../.gitbook/assets/image%20%28525%29.png)

### 将公众号信息填入 Authing 控制台

在微信公众平台创建完服务号之后，请按照下图指示在 Authing 控制台找到 Github 社会化登录配置：

![](../../.gitbook/assets/image%20%28534%29.png)

在此需要填入你的公众号信息：

* AppID: 公众号的 AppID
* AppSecret: 公众号的 App Secret
* Redirect：**这是你的业务回调域名**。比如你的网站域名是 https://example.com ， 处理 Authing 回调请求的 url 为 /auth/wechatmp/callback ， 那么你应该填写为 `https://example.com/auth/github/callback`。
* Txt Filename: 上一步配置网页授权域名中下载的的 txt 文件文件名。
* Txt Content:  上一步配置网页授权域名中下载的的 txt 文件内容。
* Scopes（可选）：详情请见[配置登录授权 Scopes](social-login-scopes.md#wechat-web-browser)。

![](../../.gitbook/assets/image%20%28590%29.png)

## 开始接入

提供两种方式：手动接入与使用 Authing 提供的 SDK。

### 方式一：使用 Authing SDK

#### 1. 安装 `@authing/wxmp` 包：

```text
npm install --save @authing/wxmp
```

或者

```text
yarn add @authing/wxmp
```

你也可以直接引用 cdn 文件：

```javascript
<script src="https://cdn.jsdelivr.net/npm/@authing/wxmp/dist/authing-wxmp-sdk.min.js"></script>
```

然后通过以下方式引入：

```javascript
import AuthingWxmp from "@authing/wxmp"
```

#### 2. 初始化 AuthingWxmp

需要传入你的用户池 ID（userPoolId）。

```javascript
const authingWx = new AuthingWxmp({
  userPoolId: "YOUR_USERPOOLID"
})
```

#### 3. 获取微信授权链接

{% hint style="info" %}
此链接请在微信客户端内访问！你可以在 Web 页面上放置一个可点击的按钮或 Logo 链接到上面的链接以便用户可以点击登录。
{% endhint %}

```javascript
const url = authingWx.getAuthorizationUrl()
```

#### 4. 处理 Authing 回调请求

> Authing 的回调请求数据中包含了用户信息。

```javascript
// 跳回业务回调链接之后获取用户信息
// 若在回调页面 authingWx 未初始化，需要先初始化，具体初始化方式参考上文

const { ok, userinfo, message } = authingWx.getUserInfo()
if (ok) {
    // do with userinfo
    console.log(userinfo)
} else if (message) {
    // message 中包含了错误提示
    alert(message)
}
```

#### 5. 完整 API 列表

请见：[https://github.com/Authing/authing-wxmp-sdk](https://github.com/Authing/authing-wxmp-sdk)

### 方式二：手动接入

#### 1. 生成微信授权链接

链接格式为：https://oauth.authing.cn/oauth/wechatmp/url/:userPoolId 。其中  userPoolId 替换为你的用户池链接。

{% hint style="info" %}
访问该链接会直接 302 跳转至微信授权页面，用户同意授权之后会先经过 Authing 的服务器，之后 Authing 会将用户信息跳转到你在 Authing 控制台配置的业务回调链接。
{% endhint %}

#### 2. 处理 Authing 回调请求

上一步用户同意授权之后，会先跳转到 Authing 服务器，之后 Authing 会携带用户信息跳转到开发者在 Authing 控制台中配置的业务回调链接，并附带以下 Get 请求参数：

| 参数 | 说明 |
| :--- | :--- |
| code | 错误或成功代码，200 为成功，非 200 为失败 |
| message | 成功或错误信息 |
| data | userInfo，若 code 为非 200 不返回此参数 |

data 数据示例：

```javascript
{
    "_id": "5b88aaea349e2d0001a5b718",
    "email": "test@test.com",
    "emailVerified": false,
    "unionid": "123456",
    "oauth": "{\"login\":\"123456\",\"id\":123456,\"node_id\":\"MDQ6VXNlcjI0Njk2ODg=\",\"avatar_url\":\"https://avatars3.githubusercontent.com/u/2469688?v=4\",\"gravatar_id\":\"\",\"url\":\"https://api.github.com/users/test\",\"html_url\":\"https://github.com/test\",\"followers_url\":\"https://api.github.com/users/test/followers\",\"following_url\":\"https://api.github.com/users/test/following{/other_user}\",\"gists_url\":\"https://api.github.com/users/test/gists{/gist_id}\",\"starred_url\":\"https://api.github.com/users/test/starred{/owner}{/repo}\",\"subscriptions_url\":\"https://api.github.com/users/test/subscriptions\",\"organizations_url\":\"https://api.github.com/users/test/orgs\",\"repos_url\":\"https://api.github.com/users/test/repos\",\"events_url\":\"https://api.github.com/users/test/events{/privacy}\",\"received_events_url\":\"https://api.github.com/users/test/received_events\",\"type\":\"User\",\"site_admin\":false,\"name\":\"test\",\"company\":\"test\",\"blog\":\"http://test.com\",\"location\":\"Beijing, China\",\"email\":\"test@test.com\",\"hireable\":null,\"bio\":\"Being NO.1\",\"public_repos\":91,\"public_gists\":0,\"followers\":109,\"following\":27,\"created_at\":\"2012-10-02T06:38:50Z\",\"updated_at\":\"2018-07-23T05:51:23Z\"}",
    "registerMethod": "oauth:wechatmp",
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

恭喜你，此时已经接入了微信 App 内网页登录。获取到用户信息之后，你可以得到登录凭证 token，你可以在后续的 API 请求中携带上此 token, 然后在后端接口中根据此 token 区分不同用户，详情请见[验证 token](../../advanced/verify-jwt-token.md#yan-zheng-authing-qian-fa-de-token)。

