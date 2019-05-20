# 常见问题

## 如何获取 Client Id 和 Client Secret ？

![](../.gitbook/assets/image%20%2816%29.png)

## Client secret 如何使用？

1. [在服务端初始化 Authing SDK for Web](https://learn.authing.cn/authing/sdk/authing-sdk-for-web#fu-wu-duan)
2. [验证 JWT Token 合法性](https://learn.authing.cn/authing/advanced/authentication/jwt-token#yan-zheng-token-he-fa-xing)

## 什么是 JWT Token？

{% page-ref page="../advanced/authentication/jwt-token.md" %}

## 如何验证 JWT Token 合法性？

请参考：[验证 JWT Token 合法性](https://learn.authing.cn/authing/advanced/authentication/jwt-token#yan-zheng-token-he-fa-xing)。

## 如何使用小程序扫码认证？

{% page-ref page="../advanced/wxapp-qrcode.md" %}

## 如何接入社会化登录

{% page-ref page="../advanced/social-login.md" %}

## 如何接入单点登录？

你可以通过选择以下三种方式的任意一个：

{% page-ref page="../advanced/oauth2/" %}

{% page-ref page="../advanced/oidc/" %}

{% page-ref page="../advanced/ldap.md" %}

## 如何配置用户权限？

{% page-ref page="../advanced/roles.md" %}

## Authing 支持哪些标准协议？

**Authing 使用的行业标准协议包括：**

* **OAuth 2.0:** 一种授权标准，允许用户在一个站点向其他站点授予对其资源的有限访问权限，而无需获得其凭证（通常是账号密码）。举个例子，你在手机上点击「使用微信登录」时都会使用此标准，并且系统会询问你是否同意与该应用共享你的头像、昵称等数据。
* **Open ID Connect:** 这是 OAuth 2.0 的一个超集，他在 OAuth 2.0 之上提供了更多用户信息和获取权限和标准，比如他定义了用户的头像为 `picture`。
* **JSON Web Tokens:** 一种开放标准，主要用来安全的传输信息，他的格式非常紧凑和独立，解析之后是一种 JSON 格式。
* **Security Assertion Markup Language \(SAML\):** 一种基于 XML 的开放数据格式，SAML 允许企业应用程序和内部、外部程序无缝连接。
* **LDAP:** LDAP 是轻量目录访问协议，英文全称是 Lightweight Directory Access Protocol，一般都简称为 LDAP。你可以把它理解为一个树型的用来存储用户和组织信息的数据库，常被用来做单点登录（ SSO ）和企业员工信息管理。

## Authing 支持哪些认证方式？

{% page-ref page="../advanced/authentication/" %}



