---
description: 实现第一个基于 Authing 的应用。
---

# 第一个应用

## 应用开发 —— 从 Authing 开始

{% hint style="info" %}
本文是一个使用 Authing 开发一个任意端应用的指南（本文以 Web 端为例），其中包含在后端验证用户合法性的内容，所以任何开发者都可以阅读本文学习 Authing 的使用方法。
{% endhint %}

{% hint style="success" %}
本文使用 Authing 实现一个基于 Web 的登录退出应用，同时为了简单起见，本文不使用任何高级前端框架。
{% endhint %}

## 开发前准备

### 学习目标

1. 学会在控制台中创建 Authing 应用
2. 使用 Authing 实现登录和退出

### 预备知识

1. 基本的 HTML 和 CSS 知识
2. 中级 JavaScript 技能

### 所需工具

1. 你喜欢的文本编辑器
2. 可以在本地运行的 Web 服务器（比如：`npm install http-server -g`）

本文的全部代码可以在 [Github](https://github.com/Authing/getting-started) 上找到，你也可以[点击这里查看线上 DEMO](https://sample.authing.cn/)。

## 第零步：注册 Authing 账号并创建一个应用

如果你还没有账号，请[点击这里注册 Authing 账号](https://authing.cn/login)，注册完成后请创建一个应用：

![&#x70B9;&#x51FB;&#x300C;+ &#x521B;&#x5EFA;&#x5E94;&#x7528;&#x300D;](../.gitbook/assets/image%20%2845%29.png)

![&#x586B;&#x5199;&#x5E94;&#x7528;&#x540D;&#x79F0;&#xFF0C;&#x7C7B;&#x578B;&#x9009;&#x62E9; Web &#x7C7B;&#x578B;](../.gitbook/assets/image%20%28284%29.png)

![&#x521B;&#x5EFA;&#x6210;&#x529F;&#x540E;&#x5373;&#x53EF;&#x8FDB;&#x5165;&#x5E94;&#x7528;&#x63A7;&#x5236;&#x53F0;&#xFF08;&#x7A7A;&#x7A7A;&#x5982;&#x4E5F;&#xFF09;](../.gitbook/assets/image%20%2879%29.png)

如果你有很多独立的系统，那么可以创建多个「用户池」来分割用户，如果你想多个应用共用一个用户池，请学习我们的[单点登录](https://learn.authing.cn/authing/quickstart/basic#dan-dian-deng-lu)概念。

## 第一步：配置一个基本的 HTML 页面和 CSS

创建一个空白的 HTML 文档用来编写 Authing 程序：

```markup
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Authing Hello World</title>
  <style>
    .btn {
        background-color: #45bbee;
        border: none;
        border-radius: 4px;
        padding: 5px 10px;
        color: #fff;
    }
    .btn:hover {
        background-color: #45aaff;
        cursor: pointer;
    }
  </style>  
</head>
<body>
  <script src="https://cdn.jsdelivr.net/npm/jquery"></script>
  <script>
      // Custom Codes
  </script>
</body>
</html>
```

示例中使用了 jQuery。

本教程只是为了演示，因此我们没选择其他更加高级的框架，这可以让我们专注于 Authing 本身。

## 第二步：增加登录状态 UI 元素

增加一段 HTML 到 HTML 页面中，用处是显示已经登录用户的用户名和完整用户信息（一段 JSON 字符串），并且又一个「退出」按钮可以用来退出。

```markup
<div id="logout" style="display: none">
    你已登录，用户名为：<span id="user"></span>。
    <div>
        完整用户信息：
        <code id="userInfo"></code>
    </div>
    <button class="btn" id="logout-btn" style="margin-top:11px">退出</button>
</div>
```

## 第三步：增加 Authing 身份认证表单

[Guard](https://github.com/Authing/Guard) 库可以帮助我们快速生成用来验证用户身份的表单，你只需要添加此库的 CDN 就可以使用：

```markup
<script src="https://cdn.jsdelivr.net/npm/@authing/guard/dist/Guard.umd.min.js"></script>
```

将脚本文件放到入口脚本之前即可。

使用该库可以生成一个登录表单，其样式如下图所示：

![&#x767B;&#x5F55;&#x8868;&#x5355;](../.gitbook/assets/image%20%28208%29.png)

调用方法非常简单，代码如下所示：

```javascript
const form = new Guard('AUTHING_CLIENT_ID', {
  // 时间戳，不填则默认生成当前时间戳
  timestamp: Math.round(new Date() / 1000),
  // 随机数，不填则默认生成随机数
  nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
});
```

所需参数解释如下：

* **AUTHING\_CLIENT\_ID**
  * 用户池 ID，可从 [Authing 控制台](https://authing.cn/dashboard) 中[获取](https://docs.authing.cn/#/quick_start/howto)。
* **timestamp**
  * 当前时间戳 `Math.round(new Date() / 1000)`
* **nonce**
  * 一个随机数字，不要超过十位数

**AUTHING\_CLIENT\_ID** 参数获取方式如下：

![](../.gitbook/assets/image%20%28107%29.png)

你可以点击这里[访问 DEMO 网站](https://sample.authing.cn/#/)。

## 第四步：监听登录成功事件并显示用户名

在 Guard 中，开发者可以使用 `.on` 方法监听登录成功的事件，[完整的事件列表请参考这里](https://github.com/authing/guard#onevent-callback)。

登录成功的事件名称为「login」：

```javascript
const form = new Guard('AUTHING_CLIENT_ID', {
  // 时间戳，不填则默认生成当前时间戳
  timestamp: Math.round(new Date() / 1000),
  // 随机数，不填则默认生成随机数
  nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
  
  title: '第一个应用' // 设置登录表单标题
});

form.on('login', function(user) {
    // 成功登录后的回调事件，参数 user 为用户数据

    localStorage.setItem('userInfo', JSON.stringify(user)); // 存储用户 id 到 localStorage 中
    localStorage.setItem('userId', user._id); // 存储用户 id 到 localStorage 中
    localStorage.setItem('username', user.username); // 存储用户 username 到 localStorage 中
    localStorage.setItem('token', user.token); // 存储用户的 JWT Token 到 localStorage 中

    form.hide(); // 为了简单起见，这里在登录成功后直接隐藏表单，在 React 或 Vue 应用中，你可以执行路由跳转或其他业务

    showLoginStatus(); // 改变 UI 状态，显示用户名和完整的用户 JSON 信息
});

const showLoginStatus = () => {
    $('#logout').show(); // 显示退出按钮
    $('#user').html(localStorage.getItem('username')); // 显示用户名
    $('#userInfo').html(localStorage.getItem('userInfo')); // 显示完整的用户信息
}

```

登录成功的回调事件中会返回登录用户的 userInfo，其中有 JWT Token，点击此处[查看 JWT Token 的释义、使用及验证](https://learn.authing.cn/authing/advanced/authentication/jwt-token)。

## 第五步：调试登录/注册功能

到第四步为止，恭喜你已经完成了登录和注册功能，现在让我们运行程序体验一下。

调试 Authing 程序需要启动一个 Web 服务器，推荐 http-server，如果你还未安装 http-server，那么请使用下面的命令进行安装：

```bash
$ npm install http-server -g
```

安装完成后请进入项目根目录然后执行以下命令：

```bash
$ http-server
```

如果你看到下列输出（端口可能不同），那么代表已经启动成功：

```bash
Starting up http-server, serving ./
Available on:
  http://127.0.0.1:8080
  http://192.168.0.103:8080
Hit CTRL-C to stop the server
```

现在让我们用浏览器打开 http://127.0.0.1:8080，应该可以看到如下界面：

![](../.gitbook/assets/image%20%28137%29.png)

此时，请先点击「注册」按钮注册一个账户：

![](../.gitbook/assets/image%20%28168%29.png)

![&#x6CE8;&#x518C;&#x6210;&#x529F;&#x540E;&#x70B9;&#x51FB;&#x300C;&#x767B;&#x5F55;&#x300D;&#x4FBF;&#x53EF;&#x4EE5;&#x767B;&#x5F55;](../.gitbook/assets/image%20%28132%29.png)

登录完成后可以看到如下信息：

![](../.gitbook/assets/image%20%28291%29.png)

登录后能看到此界面表明已经调试成功了。

## 第六步：为退出按钮增加事件

到上一步，我们完成了登录功能并体验了流程，下面，我们还需要支持退出功能：

退出功能需要用到 authing 对象的 logout 方法，authing 对象需要在 authing-load 事件中获取，代码如下：

```javascript
form.on('authing-load', async function(authing) {
    // Authing 实例加载成功后的回调函数，参数 authing 为 authing 对象

    // 使用 checkLoginStatus 方法判断当前的登录状态，需要使用 await
    // 如已经登录则隐藏登录框并显示当前的用户信息
    // 这段代码的作用是用户如果已经登录，那么刷新后还可以看到自己的用户信息
    const result = await authing.checkLoginStatus();
    if (result.status) {
        // 隐藏登录框
        form.hide();
        // 显示用户基础信息
        showLoginStatus();
    }
    
    // 使用 jQuery 监听退出按钮的点击事件
    $('#logout-btn').click(async function() {
        // 使用 logout 方法，并传入用户的 userId 进行退出
        await authing.logout(localStorage.getItem('userId'));
        alert('退出成功');
        // 刷新页面，此时可以重新看到登录框
        location.reload();
    });
})
```

最后，我们来测试下退出功能。

请打开浏览器，刷新页面，点击「退出」，稍等片刻后可以看到页面弹出了下面这个提示：

![&#x9000;&#x51FA;&#x6D89;&#x53CA;&#x5230;&#x7F51;&#x7EDC;&#x8BF7;&#x6C42;&#xFF0C;&#x53EF;&#x80FD;&#x4F1A;&#x51FA;&#x73B0;&#x7F51;&#x7EDC;&#x5EF6;&#x8FDF;&#xFF0C;&#x82E5;&#x6CA1;&#x7ACB;&#x5373;&#x51FA;&#x73B0;&#xFF0C;&#x8BF7;&#x7B49;&#x5F85;&#x4E00;&#x4E0B;&#x5373;&#x53EF;&#x770B;&#x5230;&#x63D0;&#x793A;](../.gitbook/assets/image%20%28230%29.png)

点击弹出对话框的「OK」或「确认」按钮后可以看到页面重新启用了登录框：

![](../.gitbook/assets/image%20%28289%29.png)

{% hint style="info" %}
若你需要在后端验证 JWT Token 的合法性请[点击这里查看](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。

如果你还不理解什么是 JWT Token 请[查看这篇文章](https://learn.authing.cn/authing/advanced/authentication/jwt-token)。
{% endhint %}

恭喜你，到此为止，你已经学会了如何使用 Authing 开发第一个应用。

本文的全部代码可以在 [Github](https://github.com/Authing/getting-started) 上找到，你也可以[点击这里查看线上 DEMO](https://sample.authing.cn/)。

若你想要了解更多 Guard 的使用方法，下面有几个链接可供参考：

1. [Guard Github](https://github.com/Authing/Guard)
2. [Guard 完整初始化参数列表](https://github.com/Authing/Guard#ui-%E5%AE%9A%E5%88%B6)
3. [Guard 完整事件列表](https://github.com/Authing/Guard#onevent-callback)

## 接下来你可能还需要

 学习 Authing 的基础知识以及我们会用到的专业术语：

{% page-ref page="basic.md" %}

使用 Authing 实现单点登录：

{% page-ref page="implement-sso-with-authing.md" %}

控制台是你管理所有 Authing 资源的地方，了解 Authing 控制台各模块包含的内容和你可以在控制台中做的事情：

{% page-ref page="dashboard.md" %}

了解 Authing 提供的多种部署模型，以帮助你选择该以怎样的形式部署 Authing：

{% page-ref page="deployment.md" %}

