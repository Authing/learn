---
description: Authing SSO SDK 用于发起 SSO 登录、查询 SSO 登录状态、单点登出。
---

# SDK for 单点登录

## 安装 <a id="install"></a>

### 通过 NPM 安装 <a id="npm-install"></a>

```text
$ npm install @authing/sso --save
```

接着可以通过以下方式使用

```text
import AuthingSSO from "@authing/sso";
```

### 通过 CDN 安装 <a id="cdn-install"></a>

```text
<script src="https://cdn.jsdelivr.net/npm/@authing/sso/dist/AuthingSSO.umd.min.js"></script>
<script>
  console.log(AuthingSSO);
</script>
```

## 开始使用 <a id="getting-started"></a>

需要先注册一个 [Authing](https://authing.cn/login) 账号，并[创建一个 OIDC 应用](https://docs.authing.cn/authing/advanced/oidc/create-oidc)。

### 发起登录 <a id="start-login"></a>

#### **跳转登录** <a id="jump-login"></a>

```text
import AuthingSSO from "@authing/sso";

let auth = new AuthingSSO({
  appId: "SSO_APP_ID",
  appType: "可填：oauth/oidc/saml", // 默认 oidc
  appDomain: "SSO_APP_DOMAIN"
});

// 发起单点登录，会跳转到登录页面，采用授权码模式，需要相关应用开启授权码模式
auth.login();
```

#### **窗口登录** <a id="window-login"></a>

```text
import AuthingSSO from "@authing/sso";

let auth = new AuthingSSO({
  appId: "SSO_APP_ID",
  appType: "可填：oauth/oidc/saml", // 默认 oidc
  appDomain: "SSO_APP_DOMAIN"
});

// 发起单点登录，会弹出一个窗口，里面是登录页面，采用授权码模式，需要相关应用开启授权码模式
auth.windowLogin();
```

业务域名回调地址需要托管一个 html 文件，用于将得到的 code access\_token id\_token 等参数，通过 postMessage 的方式发送给父窗口，然后将本窗口关闭。

例如，回调地址填写的是 [https://example.com/handle.html](https://example.com/handle.html%EF%BC%8C%E8%BF%99%E4%B8%AA)，这个 html 内部需要编写一段发送 postMessage 的代码，负责从 url 中取出相关参数并传递给父窗口。

Github 参考代码：[https://github.com/Authing/oidc-window](https://github.com/Authing/oidc-window)

### 查询登录状态 <a id="check-login-status"></a>

```text
let res = await auth.trackSession();
/**
 * {
 *    session: { appId: 'xxx', type: 'oidc/oauth/saml', userId: 'yyy'},
 *    userInfo: {
 *      "_id": "USER_ID",
 *      "email": "USER_EMAIL",
 *      "registerInClient": "CLIENT_ID",
 *      "token": "JTW_TOKEN",
 *      "tokenExpiredAt": "2019-10-28 10:15:32",
 *      "photo": "PICTURE",
 *      "company": "",
 *      "nickname": "NICKNAME",
 *      "username": "USERNAME",
 *   },
 *   urlParams: {
 *      code: 'xxx', // 这些参数是从 url 中获取到的，需要开发者自己存储以备使用
 *      id_token: 'ID_TOKEN',
 *      access_token: 'ACCESS_TOKEN'
 *   }
 * }
 *
 * 如果 session 不存在，返回：
 *
 * {
 *   session: null
 * }
 * */
```

### 登出 <a id="logout"></a>

```text
let res = await auth.logout();
/**
 * {
 *    message: "单点登出成功",
 *    code: 200
 * }
 * */
```

## API

### AuthingSSO.prototype.constructor

构造函数，接受一个对象作为参数。对象中的参数列表如下：

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;&#x540D;</th>
      <th style="text-align:left">&#x662F;&#x5426;&#x5FC5;&#x586B;</th>
      <th style="text-align:left">&#x63CF;&#x8FF0;</th>
      <th style="text-align:left">&#x9ED8;&#x8BA4;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">appId</td>
      <td style="text-align:left">&#x662F;</td>
      <td style="text-align:left">SSO &#x5E94;&#x7528;&#x7684; ID</td>
      <td style="text-align:left">-</td>
    </tr>
    <tr>
      <td style="text-align:left">appDomain</td>
      <td style="text-align:left">&#x662F;</td>
      <td style="text-align:left">SSO &#x5E94;&#x7528;&#x57DF;&#x540D;&#xFF0C;&#x4F8B;&#x5982; <code>app1.authing.cn</code>
      </td>
      <td style="text-align:left">-</td>
    </tr>
    <tr>
      <td style="text-align:left">appType</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">SSO &#x5E94;&#x7528;&#x7684; &#x7C7B;&#x578B;&#xFF0C;&#x53EF;&#x9009;&#x503C;&#x4E3A; <code>oidc</code>&#xFF0C;<code>oauth</code>&#xFF0C;<code>saml</code>
      </td>
      <td style="text-align:left"><code>oidc</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">scope</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">SSO &#x6388;&#x6743;&#x57DF;</td>
      <td style="text-align:left">&apos;openid profile email phone&apos;&#xFF0C;&#x67E5;&#x770B;<a href="https://docs.authing.cn/authing/advanced/oidc/oidc-params#scope-can-shu-dui-ying-de-yong-hu-xin-xi">&#x652F;&#x6301;&#x7684; scope</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">state</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">&#x81EA;&#x5B9A;&#x4E49;&#x5B57;&#x7B26;&#x4E32;&#xFF0C;&#x56DE;&#x8C03;&#x5730;&#x5740;&#x4E5F;&#x4F1A;&#x53D7;&#x5230;&#x6B64;&#x53C2;&#x6570;&#xFF0C;&#x5185;&#x5BB9;&#x76F8;&#x540C;&#xFF0C;&#x53EF;&#x7528;&#x4E8E;&#x4F20;&#x9012;&#x4FE1;&#x606F;</td>
      <td
      style="text-align:left">&#x968F;&#x673A;&#x5B57;&#x7B26;&#x4E32;</td>
    </tr>
    <tr>
      <td style="text-align:left">host</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">&#x4E00;&#x4E2A;&#x5BF9;&#x8C61;&#xFF0C;&#x7528;&#x4E8E;&#x6307;&#x5B9A;
        GraphQL &#x5730;&#x5740;</td>
      <td style="text-align:left">
        <p><code>{ </code>
        </p>
        <p><code>  oauth: &apos;https://oauth.authing.cn/graphql&apos; }</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">host.oauth</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">GraphQL &#x901A;&#x4FE1;&#x5730;&#x5740;</td>
      <td style="text-align:left">https://oauth.authing.cn/graphql</td>
    </tr>
    <tr>
      <td style="text-align:left">responseType</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">SSO &#x5E94;&#x7528;&#x6388;&#x6743;&#x6D41;&#x7A0B;&#xFF0C;&#x53EF;&#x9009;&#x503C;&#x4E3A; <code>code</code>&#xFF0C;<code>implicit</code>
      </td>
      <td style="text-align:left"><code>code</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">redirectUrl</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">SSO &#x5E94;&#x7528;&#x56DE;&#x8C03;&#x57DF;&#x540D;</td>
      <td style="text-align:left">&#x5728; Authing &#x63A7;&#x5236;&#x53F0;&#x914D;&#x7F6E;&#x7684;&#x7B2C;&#x4E00;&#x4E2A;&#x4E1A;&#x52A1;&#x57DF;&#x540D;</td>
    </tr>
    <tr>
      <td style="text-align:left">nonce</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">&#x968F;&#x673A;&#x6570;</td>
      <td style="text-align:left">&#x968F;&#x673A;&#x6570;</td>
    </tr>
    <tr>
      <td style="text-align:left">timestamp</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">&#x65F6;&#x95F4;&#x6233;</td>
      <td style="text-align:left">&#x5F53;&#x524D;&#x65F6;&#x95F4;&#x6233;</td>
    </tr>
  </tbody>
</table>示例

```text
let auth = new AuthingSSO({
  appId: "SSO_APP_ID",
  appType: "oidc",
  appDomain: "SSO_APP_DOMAIN"
});
```

### AuthingSSO.prototype.login

示例

```text
auth.login();
```

### AuthingSSO.prototype.trackSession

示例

```text
let res = await auth.trackSession();
/**
 * {
 *    session: { appId: 'xxx', type: 'oidc/oauth/saml', userId: 'yyy'},
 *    userInfo: {
 *      "_id": "USER_ID",
 *      "email": "USER_EMAIL",
 *      "registerInClient": "CLIENT_ID",
 *      "token": "JTW_TOKEN",
 *      "tokenExpiredAt": "2019-10-28 10:15:32",
 *      "photo": "PICTURE",
 *      "company": "",
 *      "nickname": "NICKNAME",
 *      "username": "USERNAME",
 *   },
 *   urlParams: {
 *      code: 'xxx', // 这些参数是从 url 中获取到的，需要开发者自己存储以备使用
 *      id_token: 'ID_TOKEN',
 *      access_token: 'ACCESS_TOKEN'
 *   }
 * }
 *
 * 如果 session 不存在，返回：
 *
 * {
 *   session: null
 * }
 * */
```

### AuthingSSO.prototype.logout

示例

```text
let res = await auth.logout();
/**
 * {
 *    message: "单点登出成功",
 *    code: 200
 * }
 * */
```

## 获取帮助 <a id="get-help"></a>

1. Join us on Gitter: [\#authing-chat](https://gitter.im/authing-chat/community)

