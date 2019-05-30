# Login-Form for Web

Login-Form 是一种可嵌入的登录表单，可根据你的需求进行配置，建议用于单页面应用程序。 它使你可以轻松添加各种社会化登录方式，以便你的用户可以无缝登录，并且在不同平台拥有一致的登录体验。

该表单拥有以下基本功能：

* 邮箱-密码/手机-验证码/认证；
* 忘记密码以及重置密码；
* 记住账号功能（加密存储到浏览器本地）；
* 社会化登录，如 Github 登录、微信登录（[需先在后台配置](https://learn.authing.cn/authing/advanced/social-login)）；
* 小程序扫码登录（[需先在后台配置](https://learn.authing.cn/authing/advanced/authentication/wxapp-qrcode)）；
* 响应式特性（兼容移动端和 PC 端）；
* 使用 LDAP 登录（[什么是 LDAP？](https://learn.authing.cn/authing/advanced/sso/ldap)）。

![](../.gitbook/assets/image%20%2844%29.png)

{% hint style="info" %}
访问 [https://sample.authing.cn](https://sample.authing.cn) 可以体验 Login-Form。
{% endhint %}

## 使用方法

### 1. 引入代码

目前 `Login-Form` 只支持 **script** 引入，暂不支持 **NPM** 安装，使用了本功能的开发者可以不必再安装 [**authing-js-sdk**](https://learn.authing.cn/authing/sdk/sdk/authing-sdk-for-web)，因为通过 script 标签引入的代码中包含了 [**authing-js-sdk**](https://learn.authing.cn/authing/sdk/sdk/authing-sdk-for-web)。

```markup
<script src="https://cdn.authing.cn/sdk/javascript/authing-login-form-1.7.1.js"></script>
```

#### 移动端

如果你的用户定位为移动用户，Authing 建议你将以下标签添加到应用程序的头部：

```javascript
<meta name="viewport" content="width=device-width, initial-scale=1"/>
```

### 2. 显示表单

首先， 你需要初始化一个新的 AuthingForm 对象，并提供 Authing ClientId（每个 Authing 应用的唯一客户端 ID，你可以从控制台中获取），如下所示：

```javascript
const form = new AuthingForm({
  // 必填，client ID
  clientId: 'your_client_id',
  // 必填，timestamp
  timestamp: Math.round(new Date() / 1000),
  // 必填，nonce
  nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
});
```

完成这两步后就可以使用表单了。

### 3. 监听登录事件以获取用户信息

你可以使用 `.on` 方法监听 Login-Form 的事件，如：

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

请在 Authing 控制台中配置好小程序扫码登录的信息，LoginForm 就会自动显示扫码登录的界面，配置方式请参考：[配置小程序信息](https://learn.authing.cn/authing/advanced/wxapp-qrcode#pei-zhi-xiao-cheng-xu-xin-xi)。

![&#x7531; LoginForm &#x81EA;&#x52A8;&#x751F;&#x6210;&#x7684;&#x5C0F;&#x7A0B;&#x5E8F;&#x626B;&#x7801;&#x767B;&#x5F55;&#x754C;&#x9762;](../.gitbook/assets/image%20%28156%29.png)

### 处理回调事件

初始化 LoginForm 后可监听 `scanning` 事件取回用户信息，如下所示：

```javascript
const form = new AuthingForm({
  // 必填，client ID
  clientId: 'your_client_id',
  // 必填，timestamp
  timestamp: Math.round(new Date() / 1000),
  // 必填，nonce
  nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
});

form.on('scanning', (userInfo) => {
  // 使用 userInfo 以及 userInfo.token 等信息
});
```

{% hint style="info" %}
验证 Token 合法性请参考：[验证 Token 合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。
{% endhint %}

## 显示和隐藏表单

Login-Form 提供了两个方法用以操作界面的显示和隐藏，方法名见下表：

| 方法名称 | 方法参数 | 功能 |
| :--- | :--- | :--- |
| **show** | **mountId** | 指定 Authing form 将在何处显示，接受一个 html 元素 id，不含`#`号。不指定则默认全屏弹出 Modal 登录框 |
| **hide** | 无 | 隐藏表单 |

在初始化完构造函数后会自动执行 `show` 方法，如：

```javascript
const form = new AuthingForm({ ... });
form.hide();
```

## 自定义选项

Login-Form 的构造函数 `AuthingForm` 提供了一些高级功能。

以下是完整的参数列表：

| 参数名称 | 是否必填 | 默认值 | 类型 | 参数说明 | 回调参数 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **clientId** | **是** | 无 | String | Authing Client ID | - |
| **secret** | **是** | 无 | String | Authing Client Secret | - |
| mountId | 否 | 无 | String | 指定 Authing form 将在何处显示，接受一个 html 元素 id，不含`#`号。不指定则默认全屏弹出 Modal 登录框 | - |
| title | 否 | Authing | String | **产品名称** | - |
| logo | 否 | \[Authing LOGO\] | String | **产品logo**，默认为 Authing 的官方 Logo | - |
| forceLogin | 否 | false | Boolean | **是否将注册和登录合并**，合并后如果用户不存在将自动注册 | - |
| hideQRCode | 否 | false | Boolean | **是否隐藏小程序扫码登录**，在开发者在 Authing 控制台开启小程序扫码登录后，若此项为 true 将不显示小程序扫码登录 | - |
| hideUP | 否 | false | Boolean | **是否隐藏用户名-密码登陆**，隐藏后将不显示用户名-密码登录框 | - |
| hideRegister | 否 | false | Boolean | **是否隐藏注册框**，隐藏后将不显示注册框 | - |
| hideUsename | 否 | false | Boolean | **是否隐藏注册时的用户名填写**，隐藏后将不显示用户名输入框 | - |
| hideOAuth | 否 | false | Boolean | **是否隐藏第三方 OAuth 登录**，在开发者在 Authing 控制台开启 OAuth 登录后，若此项为 true 将隐藏全部 OAuth 登录 | - |
| hideClose | 否 | false | Boolean | **是否隐藏登录框右上角的关闭按钮**，如果隐藏，用户将不能通过点击按钮或按 ESC 关闭登录框 | - |
| **placeholder** | 否 | {} | Object | **定制输入框的 paceholder** | - |
| **placeholder**.username | 否 | 请输入用户名 | String | **定制输入框的 paceholder** | - |
| **placeholder**.email | 否 | 请输入邮箱 | String | **用户名输入框的 paceholder** | - |
| **placeholder**.password | 否 | 请输入密码 | String | **邮箱输入框的 paceholder** | - |
| **placeholder**.confirmPassword | 否 | 请确认密码 | String | **密码输入框的 paceholder** | - |
| **placeholder**.verfiyCode | 否 | 请输入验证码 | String | **验证码输入框的 paceholder** | - |
| **placeholder**.newPassword | 否 | 请输入新密码 | String | **新密码输入框的 paceholder** | - |
| **qrcodeScanning** | 否 | {} | Object | **小程序扫码登录的配置项** | - |
| **qrcodeScanning**.redirect | 否 | true | Boolean | **是否执行跳转（在用户后台配置的URL）**，若值为false，用户数据会通过 onSuccess 回调函数返回 | - |
| **qrcodeScanning**.interval | 否 | 1500 | Number | 每隔多少秒检查一次是否扫码，默认1500 | - |
| **qrcodeScanning**.tips | 否 | 使用 微信 或小程序 身份管家 扫码登录 | String | 提示信息，可写HTML | - |
| **host** | 否 | {} | Object | **小程序扫码登录的配置项** | - |
| **host**.user | 否 | \[Authing 官方链接\] | String | **GraphQL 链接**，默认 Authing 官方链接，此处用于私有部署 Authing 的用户使用 | - |
| **host**.oauth | 否 | \[Authing 官方链接\] | String | **GraphQL 链接**，默认 Authing 官方链接，此处用于私有部署 Authing 的用户使用 | - |

## 回调事件

在初始化 `AuthingForm` 后，可使用 `on`方法，如：

```javascript
const form = new AuthingForm({ clientId: 'xxxx' });
form.on('login', function(user) {
    // 成功登录后的回调事件，参数 user 为用户数据
});
```

完整的事件列表如下：

| 事件名称 | 事件说明 | 事件参数 | 事件参数说明 |
| :--- | :--- | :--- | :--- |
| authingLoad | Authing Client ID 和 Secret验证通过，加载完成 | authing | authing 对象，可直接操作 `login`,`register`等方法 |
| authingUnload | Authing Client ID 和 Secret验证失败 | `error` | 错误信息 |
| oauthLoad | OAuth列表加载完成 | oauthList | 完整的 OAuth 列表，若用户未在后台配置过则为空 |
| oauthUnload | OAuth列表加载失败 | `error` | 错误信息 |
| login | 用户登录成功（扫码成功，请监听 scanning 事件） | userInfo | 用户数据 |
| loginError | 用户登录失败 | `error` | 错误信息，包含字段缺失／非法或服务器错误等信息 |
| register | 用户注册成功 | userInfo | 用户数据 |
| registerError | 用户注册失败 | `error` | 错误信息，包含字段缺失／非法或服务器错误等信息 |
| emailSent | 忘记密码邮件发送成功 | data | 发送的结果 |
| emailSentError | 忘记密码邮件发送失败 | `error` | 错误信息 |
| resetPassword | 重置密码成功 | data | 重置密码结果 |
| resetPasswordError | 重置密码失败 | `error` | 错误信息 |
| scanning | 扫码登录成功 | userInfo | 用户数据 |
| scanningError | 扫码登录失败 | `error` | 错误信息 |
| scanningIntervalStarting | 开始监听扫码事件 | interval | 用户可使用 `clearInterval` 停止监听 |
| formClosed | Login Form 关闭事件 | null | 用户按下 ESC 或点击右上方的关闭按钮后会触发此事件 |

## 接下来你可能还需要

对比与自己开发 UI 的优势以理解为什么要使用 Login-Form：

{% page-ref page="loginform-vs-custom-ui.md" %}

