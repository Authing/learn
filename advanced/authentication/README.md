---
description: 了解 Authing 的认证方式
---

# 认证和授权

认证指确认访问者身份，Authing 分为两种主要的认证方式，分别为：

## 普通认证

普通认证是在创建 Authing 用户池后默认支持的认证方式，此种认证方式在认证成功后会返回由 Authing 签发的 JWT Token 作为认证凭据（什么是 JWT Token？）。

{% page-ref page="zhang-hao-mi-ma-ren-zheng.md" %}

{% page-ref page="yong-hu-ming-mi-ma-ren-zheng.md" %}

{% page-ref page="shou-ji-yan-zheng-ma-ren-zheng.md" %}

{% page-ref page="xiao-cheng-xu-sao-ma-ren-zheng.md" %}

## 标准协议认证

标准协议认证指通过 OAuth 2.0、OIDC、SAML、LDAP 等标准协议认证的过程，**若你的应用安全性要求较高建议使用此种认证方式**。

{% page-ref page="../connections/she-hui-hua-deng-lu.md" %}

{% page-ref page="../connections/enterprise/pei-zhi-oauth-2.0.md" %}

{% page-ref page="../connections/enterprise/pei-zhi-oidc.md" %}

{% page-ref page="../connections/enterprise/pei-zhi-saml.md" %}

{% page-ref page="../connections/enterprise/pei-zhi-ldap.md" %}

{% page-ref page="../connections/sso.md" %}

## 如何选择认证方式



