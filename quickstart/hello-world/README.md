---
description: 实现第一个基于 Authing 的应用。
---

# 第一个应用

## 应用开发 —— 从 Authing 开始

{% hint style="info" %}
这是一个**面向前端开发者**的 Authing 应用开发指南，如果你是后端开发者请参考：
{% endhint %}

{% page-ref page="be-hello-world.md" %}

P.S. 暂无移动端 Hello World 文档。 

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

## 第零步：注册 Authing 账号并创建一个应用

## 第一步：配置一个基本的 HTML 页面

创建一个空白的 HTML 文档用来编写 Authing 程序：

```markup
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <titile>Authing Hello World</title>
</head>
<body>
  <h1>Hello World</h1>
  <script src="https://cdn.jsdelivr.net/npm/authing-js-sdk"></script>
  <script src="https://cdn.jsdelivr.net/npm/jquery"></script>
</body>
</html>
```

示例中使用了 jQuery 和 Authing。

本教程只是为了演示，因此我们没选择其他更加高级的框架，这可以让我们专注于 Authing 本身。

## 第二步：增加登录状态 UI 元素



## 第三步：增加 Authing 身份认证表单



## 第四步：增加一个登录按钮



## 第五步：增加一个退出按钮



## 第六步：显示当前用户的用户名



## 第七步：让用户名可点击



## 接下来你可能还需要

了解 Authing 是什么以及你可以将 Authing 应用到哪些场景中：

{% page-ref page="../overviews.md" %}

 学习 Authing 的基础知识以及我们会用到的专业术语：

{% page-ref page="../basic.md" %}

控制台是你管理所有 Authing 资源的地方，了解 Authing 控制台各模块包含的内容和你可以在控制台中做的事情：

{% page-ref page="../dashboard.md" %}

了解 Authing 提供的多种部署模型，以帮助你选择该以怎样的形式部署 Authing：

{% page-ref page="../deployment.md" %}

