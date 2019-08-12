# Guard for Web

Guard 是一种可嵌入的登录表单，可根据你的需求进行配置，建议用于单页面应用程序。 它使你可以轻松添加各种社会化登录方式，以便你的用户可以无缝登录，并且在不同平台拥有一致的登录体验。

该表单拥有以下基本功能：

* 邮箱-密码/手机-验证码/认证；
* 忘记密码以及重置密码；
* 记住账号功能（加密存储到浏览器本地）；
* 社会化登录，如 Github 登录、微信登录（[需先在后台配置](https://learn.authing.cn/authing/advanced/social-login)）；
* 小程序扫码登录（[需先在后台配置](https://learn.authing.cn/authing/advanced/authentication/wxapp-qrcode)）；
* 响应式特性（兼容移动端和 PC 端）；
* 使用 LDAP 登录（[什么是 LDAP？](https://learn.authing.cn/authing/advanced/sso/ldap)）。

![](../.gitbook/assets/image%20%2862%29.png)

{% hint style="info" %}
访问 [https://sample.authing.cn](https://sample.authing.cn) 可以体验 Guard。
{% endhint %}

## 使用方法

### 1. 引入代码

#### 通过 CDN 安装

通过 script 标签使用本功能的开发者可以不必再安装 [**authing-js-sdk**](https://learn.authing.cn/authing/sdk/sdk/authing-sdk-for-web)，因为通过 script 标签引入的代码中包含了 [**authing-js-sdk**](https://learn.authing.cn/authing/sdk/sdk/authing-sdk-for-web)。

```markup
<script src="https://cdn.jsdelivr.net/npm/@authing/guard/dist/Guard.umd.min.js"></script>
```

#### 通过 npm 安装

```bash
$ npm install @authing/guard --save
```

#### 移动端

如果你的用户定位为移动用户，Authing 建议你将以下标签添加到应用程序的头部：

```javascript
<meta name="viewport" content="width=device-width, initial-scale=1"/>
```

### 2. 显示表单

首先， 你需要初始化一个新的 Guard 对象，并提供 Authing ClientId（每个 Authing 应用的唯一客户端 ID，你可以从控制台中获取），如下所示：

```javascript
const form = new Guard('AUTHING_CLIENT_ID');
```

完成这两步后就可以使用表单了。

### 3. 监听登录事件以获取用户信息

你可以使用 `.on` 方法监听 Guard 的事件，如：

```javascript
// 使用 `.on` 方法，并监听 `login` 事件即可在用户登录成功后获得用户信息
form.on('login', (userInfo) => {
  localStorage.setItem('userInfo', JSON.stringify(userInfo));
  localStorage.setItem('token', JSON.stringify(userInfo.token));
});
```

如果你想监听其他事件（如：登录失败、注册失败、注册成功等），请参考[完整事件列表](https://docs.authing.cn/#/quick_start/login-form?id=%E4%BA%8B%E4%BB%B6%E5%93%8D%E5%BA%94)。

### 4. 验证 JWT Token 的合法性以及是否过期

{% page-ref page="../advanced/authentication/verify-jwt-token.md" %}

## 使用小程序扫码认证

请在 Authing 控制台中配置好小程序扫码登录的信息，Guard 就会自动显示扫码登录的界面，配置方式请参考：[配置小程序信息](https://learn.authing.cn/authing/advanced/wxapp-qrcode#pei-zhi-xiao-cheng-xu-xin-xi)。

![&#x7531; Guard &#x81EA;&#x52A8;&#x751F;&#x6210;&#x7684;&#x5C0F;&#x7A0B;&#x5E8F;&#x626B;&#x7801;&#x767B;&#x5F55;&#x754C;&#x9762;](../.gitbook/assets/image%20%28230%29.png)

### 处理回调事件

初始化 Guard 后可监听 `scanned-success` 事件取回用户信息，如下所示：

```javascript
const form = new Guard('AUTHING_CLIENT_ID');

form.on('scanned-success', (userInfo) => {
  // 使用 userInfo 以及 userInfo.token 等信息
});
```

{% hint style="info" %}
验证 Token 合法性请参考：[验证 Token 合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。
{% endhint %}

## 非 SSO 场景

开发者直接从 Authing 的用户池中获取用户数据，**不走** OAuth、OIDC 等**协议流程**。如果用户登录成功，你将在前端**直接获取**到他的数据。Guard 此场景下用于为开发者快速生成登录表单 UI，快速接入用户系统。上一节内容讲述的使用方法就是非 SSO 场景，以下是完整示例：

```markup
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Guard Example</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <script src="https://cdn.jsdelivr.net/npm/@authing/guard/dist/Guard.umd.min.js"></script>
</head>
<body>

    <div id="my-form"></div>

    <script>
        const clientId = "YOUR_AUTHING_USERPOOL_ID";
        const guard = new Guard(clientId, {
            logo: "https://usercontents.authing.cn/client/logo@2.png",
            title: "Authing",
            // 把表单插入到 id 为 my-form 的标签
            mountId: "my-form",
        });
        guard.on("authenticated", userInfo => {
            // 用户登录成功后，你可以直接在这里获取他的用户信息
            console.log(userInfo);
        });
        guard.on("register", userInfo => {
            // 用户注册成功后，你可以直接在这里获取他的用户信息
            console.log(userInfo);
        });
        guard.on("scanned-success", userInfo => {
            // 用户扫码登录成功后，你可以直接在这里获取他的用户信息
            console.log(userInfo);
        });
    </script>
</body>
</html>
```

[点击查看用户信息示例](https://docs.authing.cn/authing/sdk/authing-sdk-for-web#deng-lu)

## SSO 场景

你想为其他人**提供**身份服务或**使用**他人提供的身份服务。此部分供私有部署用户参考，使用 SaaS 的用户直接使用 Authing 云上部署的 Guard，无需操心 Guard 部署问题，只需关注[如何发起 SSO 登录请求](https://docs.authing.cn/authing/advanced/oidc/oidc-authorization#fa-qi-shou-quan)。

作为 IdP（身份提供商），**对外提供身份服务**，Guard 此场景下用于 IdP 确认用户身份。支持的协议有 OAuth 2.0，OIDC，SAML，LDAP。

作为**与其他 IdP 通信**的客户端，Guard 此场景下充当 SP 和其他 IdP 通信，用于完成相应登录协议发起授权的环节，例如向其他 SAML IdP 发送 SAML Request请求。

部署示例：

```markup
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width,initial-scale=1.0" />
    <link rel="icon" href="https://fe-static.authing.cn/dist/favicon.png" />
    <title>Authing SSO</title>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but sso doesn't work properly without JavaScript enabled.
        Please enable it to continue.</strong>
    </noscript>
    <script src="https://cdn.jsdelivr.net/npm/@authing/guard/dist/Guard.umd.min.js"></script>
    <script>
      var appId = "YOUR_AUTHING_SSO_APPID";
      var clientId = "YOUR_AUTHING_USERPOOL_ID";
      var domain = "example.authing.cn";
      const guard = new Guard(clientId, {
        appId,
        domain,
        hideClose: true,
        isSSO: true,
        SSOHost: location.origin,
        qrcodeScanning: {
          redirect: false
        },
        host: {
          user: "私有部署的内部地址",
          oauth: "私有部署的内部地址"
        }
      });
    </script>
  </body>
</html>
```

## API

### 构造函数 new Guard\(clientId, options\)

初始化一个新的 `Guard` 实例。SSO 场景和非 SSO 场景下传参方式有所不同。

#### **非 SSO 场景**

只需提供用户池 id。

* **clientId**: 用户池 id
* **options {Object}**: 允许你自定义表单的 UI，相关参数请参考 [自定义](https://github.com/Authing/Guard#自定义)

#### **SSO 场景**

供私有化部署用户参考，SaaS 用户无需操心 Guard SSO 场景部署。

需要提供用户池 id，在 options 对象中，传入你在 [Authing](https://authing.cn/dashboard) 对应应用中的 appId 和域名信息。

* **clientId**: 用户池 id
* **options {Object}**: 允许你自定义表单的 UI，相关参数请参考 [自定义](https://github.com/Authing/Guard#自定义)
* **options.appId {String}**: Authing SSO 类应用的 _appId_；
* **options.domain {String}**: Authing 中配置的 _域名_. 通常是 &lt;appDomain&gt;.authing.cn；

#### authing 对象

如果你想获取 authing 对象以调用[用户管理接口](https://docs.authing.cn/authing/sdk/authing-sdk-for-web)，请使用如下代码：

`authing-load` 事件是 `Authing` 对象初始化后的回调事件。

```javascript
guard.on('authing-load', (authing) => {
  console.log('Authing 实例初始化成功', authing);

  // authing.login
  // authing.register
  // authing.logout
  // authing.checkLoginStatus
  // ...
  // 更多请参考：https://docs.authing.cn/authing/sdk/authing-sdk-for-web
});
```

## 显示和隐藏表单

Guard 提供了两个方法用以操作界面的显示和隐藏，方法名见下表：

| 方法名称 | 方法参数 | 功能 |
| :--- | :--- | :--- |
| **show** | **mountId** | 指定 Guard 将在何处显示，接受一个 html 元素 id，不含`#`号。不指定则默认全屏弹出 Modal 登录框 |
| **hide** | 无 | 隐藏表单 |

在初始化完构造函数后会自动执行 `show` 方法，如：

```javascript
const form = new Guard('AUTHING_CLIENT_ID', { ... });
form.hide();
```

## 自定义选项

Guard 的构造函数 `Guard` 的第二个参数提供了一些高级功能。

以下是完整的参数列表：

| 参数名称 | 是否必填 | 默认值 | 类型 | 参数说明 |
| :--- | :--- | :--- | :--- | :--- |
| nonce | 否 | \[Random\] | Number | 随机数，用于防范网络攻击 |
| timpstamp | 否 | 当前时间戳 | Number | 初始化时的时间戳，用于防范网络攻击 |
| mountId | 否 | 无 | String | 指定 Guard 表单将在何处显示，接受一个 html 元素 id，不含 `#` 号。不指定则默认全屏弹出 Modal 登录框 |
| title | 否 | Authing | String | **产品名称** |
| logo | 否 | \[Authing LOGO\] | String | **产品logo**，默认为 Authing 的官方 Logo |
| forceLogin | 否 | false | Boolean | **是否将注册和登录合并**，合并后如果用户不存在将自动注册 |
| hideQRCode | 否 | false | Boolean | **是否隐藏小程序扫码登录**，在开发者在 Authing 控制台开启小程序扫码登录后，若此项为 true 将不显示小程序扫码登录 |
| hideUP | 否 | false | Boolean | **是否隐藏用户名-密码登陆**，隐藏后将不显示用户名-密码登录框 |
| hideUsername | 否 | false | Boolean | **是否隐藏注册时的用户名填写**，隐藏后将不显示用户名输入框 |
| hideRegister | 否 | false | Boolean | **是否隐藏注册框**，隐藏后将不显示注册框 |
| hideSocial | 否 | false | Boolean | **是否隐藏社会化登录**，在开发者在 Authing 控制台开启社会化登录后，若此项为 true 将隐藏全部社会化登录 |
| hideClose | 否 | false | Boolean | **是否隐藏登录框右上角的关闭按钮**，如果隐藏，用户将不能通过点击按钮或按 ESC 关闭登录框 |
| **placeholder** | 否 | {} | Object | **定制输入框的 paceholder** |
| **placeholder**.username | 否 | 请输入用户名 | String | **定制输入框的 paceholder** |
| **placeholder**.email | 否 | 请输入邮箱 | String | **用户名输入框的 paceholder** |
| **placeholder**.password | 否 | 请输入密码 | String | **邮箱输入框的 paceholder** |
| **placeholder**.confirmPassword | 否 | 请确认密码 | String | **密码输入框的 paceholder** |
| **placeholder**.verfiyCode | 否 | 请输入验证码 | String | **验证码输入框的 paceholder** |
| **placeholder**.newPassword | 否 | 请输入新密码 | String | **新密码输入框的 paceholder** |
| **placeholder**.phone | 否 | 请输入手机号 | String | **手机号输入框的 paceholder** |
| **placeholder**.phoneCode | 否 | 4 位验证码 | String | **手机验证码输入框的 paceholder** |
| **qrcodeScanning** | 否 | {} | Object | **小程序扫码登录的配置项** |
| **qrcodeScanning**.redirect | 否 | true | Boolean | **是否执行跳转（在用户后台配置的URL）**，若值为false，用户数据会通过 onSuccess 回调函数返回 |
| **qrcodeScanning**.interval | 否 | 1500 | Number | 每隔多少秒检查一次是否扫码，默认1500 |
| **qrcodeScanning**.tips | 否 | 使用 微信 或小程序 身份管家 扫码登录 | String | 提示信息，可写HTML |

**以下供私有部署用户查看**

| 参数名称 | 是否必填 | 默认值 | 类型 | 参数说明 |
| :--- | :--- | :--- | :--- | :--- |
| **useSelfWxapp** | 否 | false | Boolean | 是否使用私有部署的小程序提供二维码，此选项仅供私有部署用户使用 |
| **host** | 否 | {} | Object | **GraphqlQL 通讯地址配置项** |
| **host**.user | 否 | \[Authing 官方链接\] | String | **GraphQL 链接**，默认 Authing 官方链接，此处用于私有部署 Authing 的用户使用 |
| **host**.oauth | 否 | \[Authing 官方链接\] | String | **GraphQL 链接**，默认 Authing 官方链接，此处用于私有部署 Authing 的用户使用 |
| **appId** | 是 | 无 | String | SSO 应用的云上 ID |
| **domain** | 否 | 无 | String | SSO 类应用云上域名 |
| protocol | 否 | oauth | String | 用于显示指定 SSO 应用类型，可选值为 oauth、oidc、saml |
| SSOHost | 否 | [https://sso.authing.cn](https://sso.authing.cn) | String | SSO 类应用的通讯地址，默认 Authing 官方链接，此处用于私有部署 Authing 的用户使用 |
| isSSO | 否 | false | Boolean | 用于标识当前是否为 SSO 模式，如果只是想简单生成登录表单，使用默认 false 即可 |

## 回调事件 on\(event, callback\)

在初始化 `Guard` 后，可使用 `on`方法，如：

```javascript
const form = new Guard('AUTHING_CLIENT_ID');
form.on('login', function(user) {
    // 成功登录后的回调事件，参数 user 为用户数据
});
```

Guard 会在以下生命周期中触发相应事件，完整的事件列表如下：

| 事件名称 | 事件说明 | 事件参数 | 事件参数说明 |
| :--- | :--- | :--- | :--- |
| authing-load | Authing Client ID 验证通过，加载完成 | authing | authing 对象，可直接操作 `login`,`register` 等方法 |
| authing-unload | Authing Client ID 验证失败 | `error` | 错误信息 |
| social-load | 社会化登录列表加载完成 | oauthList | 完整的 OAuth 列表，若用户未在后台配置过则为空 |
| social-unload | 社会化登录列表加载失败 | `error` | 错误信息 |
| login/authenticated | 用户登录成功 | user | 用户数据 |
| login-error/authenticated-error | 用户登录失败 | `error` | 错误信息，包含字段缺失／非法或服务器错误等信息 |
| register | 用户注册成功 | user | 用户数据 |
| register-error | 用户注册失败 | `error` | 错误信息，包含字段缺失／非法或服务器错误等信息 |
| email-sent | 忘记密码邮件发送成功 | data | 发送的结果 |
| email-sent-error | 忘记密码邮件发送失败 | `error` | 错误信息 |
| reset-password | 重置密码成功 | data | 重置密码结果 |
| reset-password-error | 重置密码失败 | `error` | 错误信息 |
| scanned-success | 扫码登录成功 | user | 用户数据 |
| scanned-error | 扫码登录失败 | `error` | 错误信息 |
| scanning-interval-starting | 开始监听扫码事件 | interval | 用户可使用 `clearInterval` 停止监听 |
| form-closed | Guard 关闭事件 | null | 用户按下 ESC 或点击右上方的关闭按钮后会触发此事件 |

## 接下来你可能还需要

对比与自己开发 UI 的优势以理解为什么要使用 Guard：

{% page-ref page="loginform-vs-custom-ui.md" %}

