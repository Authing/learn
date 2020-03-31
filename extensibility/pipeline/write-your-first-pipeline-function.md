---
description: >-
  Pipeline 函数是 Authing 认证流程中执行的自定义 JavaScript 代码。借助 Pipeline，开发者可以轻易自定义和扩展
  Authing 的能力。
---

# 创建你的第一个 Pipeline 函数

本文介绍如何使用 Authing 控制台创建一个 Pipeline 函数。如果你想使用 SDK 创建，请见：

{% page-ref page="node-sdk.md" %}

非 JS 开发者请见：

{% page-ref page="graphql-api.md" %}

## 0. 注册 Authing 开发者账号 <a id="register-authing"></a>

如果你还没有 Authing 开发者账号，请[点击这里注册 Authing 账号](https://authing.cn/login)，注册完成后系统会引导你创建你的第一个用户池。同时[了解一下有关用户池的基础概念](../../quickstart/basic.md)。

## 1. 选择一个 Pipeline 模版 <a id="choose-a-template"></a>

Authing 提供了很多开箱即用的模版，帮助你快速上手。

{% hint style="success" %}
你也可以在这里查看 Pipeline 模版函数列表：[https://github.com/authing/pipeline](https://github.com/authing/pipeline)。同时欢迎你发挥才智，为我们贡献 Pipeline 模版函数。
{% endhint %}

首先进入控制台的 **用户池** - **扩展能力** - **自定义 Pipeline** 页面，你会看到一些提示：

![](../../.gitbook/assets/image%20%28207%29.png)

点击右上角 “创建 Pipeline 函数“，进入选择模版页：

![](../../.gitbook/assets/image%20%28412%29.png)

这里，我们选择访问控制中的 "注册邮箱域名白名单"。

## 2. 创建一个  Pipeline 函数 <a id="create-a-function"></a>

这里我们设置的域名白名单是 "example.com"，你也可以改成自己的。

![](../../.gitbook/assets/image%20%28407%29.png)

点击左下角的“保存“按钮，我们会将此函数部署到云端，需要一定时间，请耐心等待。

回到  Pipeline 函数列表页面，可以看到我们刚刚添加的那个函数。

![](../../.gitbook/assets/image%20%28604%29.png)

{% hint style="success" %}
如果你有多个 Pipeline 函数，可以拖拽进行排序。
{% endhint %}

## 3. 验证白名单是否有效 <a id="validate"></a>

这里我们使用 Authing 提供的表单进行登录，进入 **用户池** - **社会化登录**- **OIDC 应用**页面，你可以看到你的所有 OIDC 应用。

![](../../.gitbook/assets/image%20%28499%29.png)

 点击右边第一个按钮 “体验登录”，你会跳转到 Authing 的登录表单 Guard 的页面。

了解更多 Guard 信息，请见：

{% page-ref page="../../sdk/guard/" %}

首先使用非 `example.com` 后缀邮箱，看到返回了 "Access Denied." 提示，这是我们在自定义 Pipeline 函数中自己指定的提示信息。

![](../../.gitbook/assets/image%20%28448%29.png)

之后再使用后缀为 `example.com` 的邮箱注册，注册成功！

![](../../.gitbook/assets/image%20%28110%29.png)

## 4. 接下来 ... <a id="whats-more"></a>

你可以：

* 详细阅读 [Pipeline 函数的开发文档](pipeline-function-api-doc.md)。
* 了解可以在 pipe 函数中获取那些请求信息：[user 对象](user-object.md) 和 [context 对象](context-object.md)。
* 了解如何[在 pipe 函数中使用环境变量](env.md)。
* 了解在 pipe 函数中可以使用[哪些开箱即用的 Node Modules](available-node-modules.md)。你可以直接使用 [authing-js-sdk ](../../sdk/sdk-for-javascript/)！
* 我们提供了很多丰富的 Pipeline 函数模板，[点击这里查看](https://github.com/authing/pipeline)。
* 我们还提供了方便的[在线调试手段](how-to-debug.md)，方便开发者在线调试。
* 你还可以使用  [Node SDK](node-sdk.md)  管理自己的 Pipeline 资源。
* 非 JS 开发者，可以使用我们的 [GraphQL 调试器](graphql-api.md)，上面有详细的文档说明。



