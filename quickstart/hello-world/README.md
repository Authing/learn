---
description: 实现第一个基于 Authing 的应用。
---

# 第一个应用

## 应用开发 —— 从 Authing 开始

{% hint style="info" %}
这是一个**面向前端开发者**的 Authing 应用开发指南，如果你是后端开发者请参考：
{% endhint %}

{% page-ref page="be-hello-world.md" %}

{% hint style="success" %}
本文使用 Authing 实现一个基于 Web 的登录登出应用，同时为了简单起见，本文不使用任何高级前端框架。
{% endhint %}

## 安装 Authing SDK

### 使用 CDN

```markup
<script src="https://cdn.jsdelivr.net/npm/authing-js-sdk"></script>
```

如果你想使用 NPM 安装 SDK，请参考：

{% page-ref page="../../sdk/authing-sdk-for-web.md" %}

### 学习目标：

1. 学会创建 Authing 应用
2. 使用 Authing 实现登录和退出

### 预备知识：

1. 基本的 HTML 和 CSS 知识
2. 中级 JavaScript 技能

### 所需工具：

1. 你喜欢的文本编辑器
2. 可以在本地运行的 Web 服务器（比如：`npm install http-server -g`）

## 第零步：注册 Authing 账号并创建一个应用

如果你还没有账号，请[点击这里注册 Authing 账号](https://authing.cn/login)，注册完成后请创建一个应用：

![&#x70B9;&#x51FB;&#x300C;+ &#x521B;&#x5EFA;&#x5E94;&#x7528;&#x300D;](../../.gitbook/assets/image%20%284%29.png)

![&#x586B;&#x5199;&#x5E94;&#x7528;&#x540D;&#x79F0;&#xFF0C;&#x7C7B;&#x578B;&#x9009;&#x62E9; Web &#x7C7B;&#x578B;](../../.gitbook/assets/image%20%2829%29.png)

![&#x521B;&#x5EFA;&#x6210;&#x529F;&#x540E;&#x5373;&#x53EF;&#x8FDB;&#x5165;&#x5E94;&#x7528;&#x63A7;&#x5236;&#x53F0;](../../.gitbook/assets/image%20%288%29.png)

## 第一步：配置一个基本的 HTML 页面

创建一个空白的 HTML 文档用来编写 Authing 程序：

```markup
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Authing Hello World</title>
</head>
<body>
  <script src="https://cdn.jsdelivr.net/npm/authing-js-sdk"></script>
  <script src="https://cdn.jsdelivr.net/npm/jquery"></script>
  <script>
      // Custom Codes
  </script>
</body>
</html>
```

示例中使用了 jQuery 和 Authing。

本教程只是为了演示，因此我们没选择其他更加高级的框架，这可以让我们专注于 Authing 本身。

## 第二步：增加登录状态 UI 元素

增加 2 个 `<p>` 元素到 HTML 页面中，一个显示已经登录，另外一个显示未登录。

```markup
<p id="login">
  你尚未登录
</p>
<p id="logout">
  你已登录，用户名为：<span id="user"></span>。
</p>
```

## 第三步：增加 Authing 身份认证表单

[Login-Form](https://github.com/authing/login-form) 库可以帮助我们快速生成用来验证用户身份的表单，你只需要添加此库的 CDN 就可以使用：

```markup
<script src="https://cdn.authing.cn/sdk/javascript/authing-login-form-1.7.1.js"></script>
```

将脚本文件放到入口脚本之前即可。

该库生成的登录表单如下图所示：

![&#x767B;&#x5F55;&#x8868;&#x5355;](../../.gitbook/assets/image%20%2820%29.png)

同时你可以点击这里[访问 DEMO 网站](https://sample.authing.cn/#/)。

调用方法非常简单，代码如下所示：

```javascript
  new AuthingForm({
    // 必填，client ID
    clientId: 'your_client_id',
    // 必填，timestamp
    timestamp: Math.round(new Date() / 1000),
    // 必填，nonce
    nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
  });
```

初始化需要的 clientId 参数获取方式如下：

![](../../.gitbook/assets/image%20%2812%29.png)

## 第四步：监听登录事件

在 Login-Form 中，开发者可以使用 `.on` 的方法监听登录成功的事件。

```javascript
const form = new AuthingForm({
  // 必填，client ID
  clientId: 'your_client_id',
  // 必填，timestamp
  timestamp: Math.round(new Date() / 1000),
  // 必填，nonce
  nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
  
  title: '第一个应用' // 设置登录表单标题
});

form.on('login', function(user) {
	// 成功登录后的回调事件，参数 user 为用户数据
	
	localStorage.setItem('userInfo', JSON.stringify(user));
	localStorage.setItem('userId', user._id); // 存储用户 id 到 localStorage 中
	
	form.hide(); // 为了简单起见，这里在登录成功后直接隐藏表单，在 React 或 Vue 应用中，你可以执行路由跳转或其他业务
	
	
});
```

完整事件列表请[点击这里查看](https://github.com/Authing/login-form#%E4%BA%8B%E4%BB%B6%E5%93%8D%E5%BA%94)。

## 第五步：增加一个退出按钮



## 第六步：显示当前用户的用户名



## 接下来你可能还需要

了解 Authing 是什么以及你可以将 Authing 应用到哪些场景中：

{% page-ref page="../overviews.md" %}

 学习 Authing 的基础知识以及我们会用到的专业术语：

{% page-ref page="../basic.md" %}

控制台是你管理所有 Authing 资源的地方，了解 Authing 控制台各模块包含的内容和你可以在控制台中做的事情：

{% page-ref page="../dashboard.md" %}

了解 Authing 提供的多种部署模型，以帮助你选择该以怎样的形式部署 Authing：

{% page-ref page="../deployment.md" %}

