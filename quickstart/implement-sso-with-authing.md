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

![&#x521B;&#x5EFA;&#x7528;&#x6237;&#x6C60;](../.gitbook/assets/image%20%2870%29.png)

![&#x9009;&#x62E9;&#x5E94;&#x7528;&#x7C7B;&#x578B;](../.gitbook/assets/image%20%2856%29.png)

## 创建一个授权应用

选择**第三方登录** -&gt; **OIDC 应用**选项卡，点击「创建 OIDC 应用」按钮创建应用。

![&#x521B;&#x5EFA; ODC &#x5E94;&#x7528;](../.gitbook/assets/image%20%2847%29.png)

点击按钮后会弹出一个创建表单，如下图所示：

![&#x914D;&#x7F6E; OIDC &#x5E94;&#x7528;](../.gitbook/assets/image%20%28178%29.png)

**填写应用名称**，并指定此应用的二级域名（用户将通过此网址进行认证）和回调地址（业务地址），其他参数保留默认，然后点击「确定」。 

**参数解释**

**认证地址**，一个 authing.cn 的二级域名，用户将在此网址进行登录。

**回调 URL**，登录成功后，回调到开发者自己业务的地址。本教程为演示，填写的地址是 http://localhost:8080，实际场景下请填写自己的业务地址。

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
<script src="https://cdn.jsdelivr.net/npm/@authing/sso/dist/AuthingSSO.umd.min.js"></script>
<script>
  let auth = new AuthingSSO({
    appId: "YOUR_OIDC_APP_ID",
    appType: "oidc",
    appDomain: "OIDC_APP_DOMAIN.authing.cn"
  });
</script>
```

### 监听按钮的点击事件

达到的效果是：

* 点击 login 按钮，浏览器会跳转到登录页面，与用户完成身份确认。
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
    <script src="https://cdn.jsdelivr.net/npm/@authing/sso/dist/AuthingSSO.umd.min.js"></script>
    <script>
      let auth = new AuthingSSO({
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

在终端中运行以下命令

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

![](../.gitbook/assets/image%20%2898%29.png)

现在我们点击 login 按钮，会跳转到 OIDC 应用的用户认证页面，输入用户名密码进行登录。

![](../.gitbook/assets/image%20%28109%29.png)

浏览器被重定向到我们之前设置的回调链接。

点击 trackSession 按钮，此时能够获取到该用户的登录状态，包括用户 ID，应用 ID，应用类型，还有此用户的详细信息。

![](../.gitbook/assets/image%20%28254%29.png)

点击 logout 按钮，输出单点登出成功。

![](../.gitbook/assets/image%20%28156%29.png)

此时我们再点击 trackSession 按钮，可见登录状态为空，说明用户已经单点登出了。

![](../.gitbook/assets/image%20%28128%29.png)

## 用户自行修改资料

在 SSO 应用的用户认证页面，用户可以修改自己的资料。

用户可以访问以下链接，进入个人资料修改页面：

```text
https://<appDomain>.authing.cn/login/profile
```

其中 `<appDomain>` 是你的 SSO 应用的二级域名。

如果用户未登录，会先要求用户登录再进入个人中心；对于已登录的用户则会直接进入个人中心。

![&#x4E2A;&#x4EBA;&#x4E2D;&#x5FC3;](../.gitbook/assets/image%20%28100%29.png)

## 检验 token 合法性

Authing 内部涉及到三种 token，建议先[了解它们的用途和区别](https://docs.authing.cn/authing/advanced/oidc/oidc-params#idtokenaccesstoken-he-token-zhi-jian-de-qu-bie)，然后你可能需要了解如何验证它们的合法性。

{% page-ref page="../advanced/authentication/verify-jwt-token.md" %}

## 接下来你可能还需要

控制台是你管理所有 Authing 资源的地方，了解 Authing 控制台各模块包含的内容和你可以在控制台中做的事情：

{% page-ref page="dashboard.md" %}

了解 Authing 提供的多种部署模型，以帮助你选择该以怎样的形式部署 Authing：

{% page-ref page="deployment.md" %}



