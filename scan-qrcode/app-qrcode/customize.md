---
description: Authing 一直以来都致力于带给开发者高度自定义的开发体验，所以我们提供以下自定义配置项，开发者可以根据自己业务的需要，在安全性和便捷性之间权衡。
---

# 自定义配置项

开发者可在 [Authing 控制台](https://authing.cn/dashboard) **基础配置** -&gt; **基础设置** -&gt; **App 扫码登录 Web 自定义配置** 处自定义配置。

![](../../.gitbook/assets/image%20%28184%29.png)

### 二维码有效时间  <a id="code-expires-after"></a>

默认 120 s。 

### 是否在查询二维码状态接口返回完整用户信息 <a id="return-full-userinfo-at-polling-response"></a>

默认不返回。由于查询二维码状态接口是没有权限校验的，这意味着直接在此接口返回用户信息（包含登录凭证 token）是存在安全隐患的，所以我们推荐开发者遵循最佳实践：查询二维码状态接口只返回用户昵称和头像，使用 ticket 换取用户信息。

### ticket 有效时间 <a id="ticket-expires-at"></a>

默认为 300 s。

### 是否允许在浏览器使用 ticket 换取用户信息 <a id="allow-exchange-userrinfo-from-browser"></a>

默认不允许，需要在服务器端调用，即需要使用用户池密钥初始化之后。[点击了解如何初始化后端 SDK](https://docs.authing.cn/authing/sdk/sdk-for-node#chu-shi-hua)。

一个典型的使用场景是：用户扫码同意授权、开发者得到 ticket 之后，发送到自己的后端，使用后端 SDK 换取用户信息，之后重定向到已登录页面，同时将用户信息写入 localStroage。

