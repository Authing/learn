---
description: 使用 Authing 实现一个 Hello World。
---

# Hello World

## 应用开发 —— 从 Authing 开始

{% hint style="info" %}
这是一个**面向前端开发者**的 Authing Hello World，如果你是其他开发者请参考：
{% endhint %}

{% page-ref page="be-hello-world.md" %}

P.S. 暂无移动端 Hello World 文档。 

{% hint style="success" %}
本文将使用 Authing 实现一个简单的 Hello World 应用，这是一个 Web 前端应用（为了简单起见，本文不使用任何前端框架），想实现的效果是只有登录成功的用户才能看到「Hello World」字样。
{% endhint %}

## 安装 Authing SDK for Web

### 使用 CDN

```markup
<script src="https://cdn.jsdelivr.net/npm/authing-js-sdk"></script>
```

如果你想使用 NPM 安装 SDK，请参考：

{% page-ref page="../../sdk/authing-sdk-for-web.md" %}

## 第零步：注册 Authing 账号并创建一个应用

## 第一步：配置一个基本的 HTML 页面

创建一个空白的 HTML 文档用来编写 Authing 程序：

```markup
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Profile Viewer</title>
</head>
<body>
  <h1>Profile viewer</h1>
</body>
</html>
```

## 接下来你可能还需要

了解 Authing 是什么以及你可以将 Authing 应用到哪些场景中：

{% page-ref page="../overviews.md" %}

 学习 Authing 的基础知识以及我们会用到的专业术语：

{% page-ref page="../basic.md" %}

控制台是你管理所有 Authing 资源的地方，了解 Authing 控制台各模块包含的内容和你可以在控制台中做的事情：

{% page-ref page="../dashboard.md" %}

了解 Authing 提供的多种部署模型，以帮助你选择该以怎样的形式部署 Authing：

{% page-ref page="../deployment.md" %}

