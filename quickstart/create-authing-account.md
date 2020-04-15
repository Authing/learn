---
description: 本文引导你如何创建一个 Authing 开发者账号，并熟悉一下 Authing 系统总体运行模式。
---

# 注册 Authing 开发者账号

如果你还没有 Authing 开发者账号，请[点此注册 Authing 账号](https://sign.authing.cn/)：

![](../.gitbook/assets/image%20%28499%29.png)

## 用户池创建指引

注册完成后系统会引导你创建你的第一个**用户池**。

{% hint style="success" %}
**用户池（UserPool）**是 Authing 中最核心的概念，一个开发者账号主体可以创建多个用户池，一个用户池内存储着所有用户的原始数据，开发者基于用户池管理用户。

一个用户池可以创建多个**应用**，如 OIDC 应用、OAuth 应用、SAML Idp、LDAP 用户目录等，授权这些应用获取用户池内的用户信息。

用户池的详细介绍请见[基础概念](basic.md#yong-hu-chi)。
{% endhint %}

为你的用户池选择一个类型，如 Web 应用、iOS 应用、Android 应用、小程序等，此选项**可多选**：

{% hint style="info" %}
用户池类型不会影响用户池任何具体功能，你填写的应用类型可以帮助 Authing 更好得为你推荐 SDK 和开发工具。
{% endhint %}

![](../.gitbook/assets/image%20%28122%29.png)

### 系统会自动为你创建第一个 OIDC 应用：

你需要为你的 OIDC 应用选择一个二级域名：

![](../.gitbook/assets/image%20%28570%29.png)

之后系统为为你自动创建一个 OIDC 应用：

![](../.gitbook/assets/image%20%28400%29.png)

比如你填写的域名为 awesome, 那么你就马上拥有了 [awesome.authing.cn](https://awesome.authing.cn) 这个二级域名，你可以访问此链接体验你的应用登录表单：

![](../.gitbook/assets/image%20%28367%29.png)

之后，你可以在 用户池 - 第三方登录 - OIDC 应用看到自己的 OIDC 应用列表：

![](../.gitbook/assets/image%20%28370%29.png)

点击下图所示按钮可在线体验：

![](../.gitbook/assets/image%20%28600%29.png)

## Authing 控制台简介

接下来会为你介绍 Authing 控制台的组成，帮助你快速了解 Authing 的能力。

Authing 控制台一级菜单一共包括：

* 概览：在这里你可以查看用户池用户增长趋势、登录热点图、用户地理位置分布、注册方式统计、最近登录注册情况等。
* 用户中心：在这里管理你的用户、进行权限角色配置、设置注册白名单等。
* 消息服务：在这里配置邮件模版、第三方邮件服务、短信验证。
* 第三方登录：在这里配置你的社会化登录信息、创建 OIDC 应用、OAuth 应用、LDAP 用户目录、SAML Idp 等。
* 基础配置：在这里查看你的用户池基础配置、进行密码强度管理、自定义密码函数、单点登录配置等。
* 扩展能力：在这里可以利用 Authing 的扩展能力，实现自定义 Pipeline 函数、Webhook 等。
* SDK 接入：在这里可以查看快速集成案例、 支持的 SDK 列表。
* 用量统计：在这里查看用户池认证次数使用情况、短信使用情况等。
* 系统日志：在这里查看扫码日志、认证记录、用户池修改记录、用户信息修改记录、用户角色修改记录、白名单修改记录等。

![](../.gitbook/assets/image%20%28260%29.png)

### 基础配置

在此你可以看到你的 **用户池 ID（userPoolId）** 及**密钥（secret）**，这你之后会经常用到。

![](../.gitbook/assets/image%20%28479%29.png)

### 第三方登录

配置社会化登录，如「微信登录」、「GitHub 登录」、「小程序登录」等：

{% page-ref page="../social-login/web/" %}

{% page-ref page="../social-login/mobile/" %}

{% page-ref page="../social-login/miniprogram/" %}

配置 OIDC 应用：

{% page-ref page="../authentication/oidc/" %}

配置 OAuth 2.0 应用：

{% page-ref page="../authentication/oauth2/" %}

配置 LDAP 服务：

{% page-ref page="../authentication/ldap/ldap.md" %}

{% page-ref page="../authentication/ldap/ldap-idp.md" %}

配置 SAML 服务：

{% page-ref page="../authentication/use-saml/" %}

配置 Active Directory 登录：

{% page-ref page="../authentication/active-directory/" %}

### 扩展能力

#### Pipeline 函数

Pipeline 函数是 Authing 认证流程中执行的自定义 JavaScript 代码。借助 Pipeline，开发者可以轻易自定义和扩展 Authing 的能力。更多介绍可以看这篇文章：[《Introduce Pipeline: Authing 的可扩展性达到了新高度》](https://authing.cn/blog/Introduce-Authing-Pipeline/)。

你可以在这里了解如何快速接入：

{% page-ref page="../extensibility/pipeline/" %}

#### Webhook 

{% page-ref page="../extensibility/use-webhook.md" %}

### 协作用户池

Authing 可以为你的用户添加协作组，并给他们配置对应的权限。这样其他 Authing 开发者就也能够管理你的用户池了：

![](../.gitbook/assets/image%20%28330%29.png)

![](../.gitbook/assets/image%20%28369%29.png)

## 接下来 ...

你可以开始创建你的第一个应用了：

{% page-ref page="hello-world.md" %}

