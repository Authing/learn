# 企业微信（企业内部）扫码登录

## 准备工作 <a id="prepare"></a>

你一共需要准备以下内容：

1. [注册 Authing 开发者账号](../../quickstart/create-authing-account.md)
   * 在创建账号的引导过程中，你会得到一个二级域名，在后面会用到。
2. [注册一个企业微信账号](https://work.weixin.qq.com/)
3. 在 Authing 控制台填入企业微信相关信息

### 在 Authing  控制台找到「企业微信（内部应用）扫码登录」

![](../../.gitbook/assets/image%20%28272%29.png)

之后你需要将相关配置填入下面的表单：

![](../../.gitbook/assets/image%20%2823%29.png)

### 获取企业ID（CorpID）

在 [我的企业 - 企业信息 ](https://work.weixin.qq.com/wework_admin/frame#profile)页面可以获取到。

![](../../.gitbook/assets/image%20%28461%29.png)

### 获取 AgentID 和 Secret

在 [应用管理 - 应用管理 ](https://work.weixin.qq.com/wework_admin/frame#apps)页面的应用详情可以获取到。

![](../../.gitbook/assets/image%20%2848%29.png)

### 启用企业微信授权登录

在应用详情页，点击设置企业微信授权登录：

![](../../.gitbook/assets/image%20%28564%29.png)

在新打开的页面，设置授权回调域为 core.authing.cn 。

![](../../.gitbook/assets/image%20%2818%29.png)

### 添加网页授权信任域名

在应用详情页，设置网页授权及JS-SDK域名，填写你的 OIDC 应用二级域名，如 yourapp.authing.cn 。

![](../../.gitbook/assets/image%20%28314%29.png)

### 配置回调链接 Redirect URL

如果你使用 [Guard](../../sdk/guard/)，可以留空填 「\#」。

如果你需要手动接入企业微信扫码登录，需要填写你的**业务回调链接**，用户授权之后，Authing 将会把用户信息回调给你。

## 开始接入

### 拼接网页授权链接

{% api-method method="get" host="https://core.authing.cn/" path="oauth/wechatwork/:userPoolId/sso-qrconnect-url" %}
{% api-method-summary %}

{% endapi-method-summary %}

{% api-method-description %}
获取企业微信扫码授权登录链接，**此链接需要在浏览器内访问！**
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="userPoolId" type="string" required=true %}
用户池 ID
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
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

比如你的用户池 ID 为 5e4cdd055df3df65dc58b97d，则你需要引导你的用户跳转到[https://core.authing.cn/oauth/wechatwork/5e4cdd055df3df65dc58b97d/sso-qrconnect-url](http://foreign.holegots.com:8005/oauth/wechatwork/5e4cdd055df3df65dc58b97d/authorization-url)

{% hint style="info" %}
你可以在 Web 页面上放置一个可点击的按钮或 Logo 链接到上面的链接以便用户可以点击登录。
{% endhint %}

在浏览器中访问上述链接，你应该成功可以跳转企业微信扫码登录页面：

![](../../.gitbook/assets/image%20%28186%29.png)

### 处理 Authing 回调数据

{% hint style="success" %}
Authing 为你隐藏了和企业微信服务器的交互过程，你可以直接获取到用户信息。
{% endhint %}

上一步用户同意授权之后，会先跳转到 Authing 服务器，之后 Authing 会携带用户信息跳转到开发者在 Authing 控制台中配置的业务回调链接，并附带以下 Get 请求参数：

| 参数 | 说明 |
| :--- | :--- |
| code | 错误或成功代码，200 为成功，非 200 为失败 |
| message | 成功或错误信息 |
| data | userInfo 用户信息，若 code 为非 200 不返回此参数 |
| bindOAuth | 是否是登录操作， 无此参数或此参数为`0`则为登录, 此参数为`1`则为绑定账号操作, 此时可使用`data`参数中的数据[绑定OAuth](../../sdk/sdk-for-node/bind-social-login.md#bang-ding-she-hui-hua-zhang-hao) |

{% hint style="info" %}
Authing 回调给你的用户信息是经过加工过的，会自动注册到你的用户池，会和企业微信返回的原始数据不一样。
{% endhint %}

data （用户信息）示例：

```javascript
{
  email: '',
  phone: null,
  emailVerified: false,
  username: 'liaozhangjiang',
  nickname: 'LiaoZhangJiang',
  company: '',
  photo: 'http://wework.qpic.cn/bizmail/mKZmqeWl7K57rTUjpictGYYpRgOgv9hMibGFjXqC05RKOkEFoibOrbzCw/0',
  loginsCount: 9,
  registerMethod: 'oauth:wechatwork-qrconnect',
  blocked: false,
  isDeleted: false,
  oauth: '{"corpid":"ww736adab7f131153d","userid":"LiaoZhangJiang","deviceId":"883CBA07-87A3-46B2-94FF-6907D447D398","name":"LiaoZhangJiang","gender":"1","avatar":"http://wework.qpic.cn/bizmail/mKZmqeWl7K57rTUjpictGYYpRgOgv9hMibGFjXqC05RKOkEFoibOrbzCw/0","qr_code":"https://open.work.weixin.qq.com/wwopen/userQRCode?vcode=vcc027dee84b322352"}',
  metadata: '"{}"',
  _id: "5e7efb7ff0dfe6f2ffb64763",
  unionid: 'ww:ww736adab7f131153d:LiaoZhangJiang',
  openid: 'ww:ww736adab7f131153d:LiaoZhangJiang',
  lastIP: '::ffff:127.0.0.1',
  lastLogin: "2020-03-28T10:17:14.764Z",
  signedUp: "2020-03-28T07:23:43.764Z",
  token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiIiwidW5pb25pZCI6Ind3Ond3NzM2YWRhYjdmMTMxMTUzZDpMaWFvWmhhbmdKaWFuZyIsImlkIjoiNWU3ZWZiN2ZmMGRmZTZmMmZmYjY0NzYzIiwiY2xpZW50SWQiOiI1ZTRjZGQwNTVkZjNkZjY1ZGM1OGI5N2QifSwiaWF0IjoxNTg1MzkwNjM0LCJleHAiOjE1ODY2ODY2MzR9.Jt7ovlBgl_Lfb63lK5OWaClFDZypxUFP8J32TerBUDY',
  tokenExpiredAt: 2020-04-12T10:17:14.000Z
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

恭喜你，此时已经接入了企业微信扫码登录。获取到用户信息之后，你可以得到登录凭证 token，你可以在后续的 API 请求中携带上此 token, 然后在后端接口中根据此 token 区分不同用户，详情请见[验证 token](https://docs.authing.cn/authing/advanced/verify-jwt-token#yan-zheng-authing-qian-fa-de-token)。



