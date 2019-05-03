---
description: 学习 Authing 的基础知识以及我们会用到的一些术语。
---

# 基础概念

在科技领域，学习新知识最大的障碍就来自于「术语」，当你试图去了解一个新概念时，大量的术语将会对你的理解造成阻碍。本文将会解释 Authing 中的常用术语，以期能帮助你尽快熟悉 在 Authing 中涉及到的知识。

{% hint style="success" %}
本文将介绍**应用、账户、通信、域名和第三方登录**等概念，如果你对这些概念比较熟悉，可以放心跳过本章。

我们会假设有一家名为「非凡」的科技公司，这家公司想使用 Authing 对他们的 Web 应用进行用户名密码和微信认证，同时希望能管理自己内部的软件系统，实现员工对内部软件的单点登录。
{% endhint %}

## 应用和用户池

[注册完 Authing 账户](https://authing.cn/login)后，你看到的是一个空白页面，此时你需要创建一个应用作为你的**用户池**。拥有了一个用户池之后你可以授权其他应用（第三方应用或者你自己写的应用）读取用户数据。授权协议可以使用 OAuth2.0、OIDC、SAML 或者 LDAP（协议的具体内容请[点击这里查看](https://learn.authing.cn/authing/protocol/oauth-2.0)）。

> 如果你还不了解上一段话说的四种认证协议，先不要着急，后面会讲到。

创建应用时可以选择如何四种类型：

![&#x53EF;&#x4F9B;&#x9009;&#x62E9;&#x7684;&#x56DB;&#x79CD; Authing &#x5E94;&#x7528;&#x7C7B;&#x578B;](../.gitbook/assets/image%20%283%29.png)

每一个应用都有一个唯一的 clientId，比如「5cc59bdf1bbaf0188cce6d84」，这个 clientId 是唯一的且不可修改，主要用来标识你的用户池；同时会有一个 secret，clientId 和 secret 加在一起可以让你不经过授权直接修改用户池内的用户数据（包含注册、登录、退出、修改资料等操作），或者换个说法，如果你不了解上文介绍的四种协议，那么使用 clientId 和 secret 是最简单的方式（如何使用请参考：[Authing SDK for Web](https://learn.authing.cn/authing/others/libraries/authing-sdk-for-web)）。

clientId 和 secret 在控制台中的查看位置如下图所示：

![&#x5728;&#x5E94;&#x7528;&#x7684;&#x300C;&#x8BBE;&#x7F6E;&#x300D;&#x83DC;&#x5355;&#x4E2D;&#x53EF;&#x4EE5;&#x67E5;&#x770B; clientId &#x548C; secret](../.gitbook/assets/image%20%284%29.png)

如上图所示，如果你的 secret 不小心泄漏，可以通过「刷新」按钮重置 secret。但是请注意，如果你在某些程序中使用了该 secret，那么使用该 secret 的程序也必须要手动更新 secret，否则会造成该程序的「身份认证功能」毁坏。

{% hint style="success" %}
在本文的例子中，「非凡科技」有一个 Web 应用，所以他选择的应用类型为「Web」，并为此应用取名为「梁非凡」。
{% endhint %}

## 通信



## 域名

### 自定义域名



## 第三方登录

### 社会化登录

### 单点登录

### 企业级认证



