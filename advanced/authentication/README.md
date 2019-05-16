---
description: 了解 Authing 的认证方式
---

# 认证和授权

认证指确认访问者身份，Authing 分为两种主要的认证方式（[如何选择认证方式？](https://learn.authing.cn/authing/advanced/authentication#ru-he-xuan-ze-ren-zheng-fang-shi)），分别为：

## 普通认证

在创建 Authing 用户池后默认支持的认证方式是普通认证，此种认证方式在认证成功后会直接返回由 Authing 签发的 JWT Token 作为认证凭据（[什么是 JWT Token？](https://learn.authing.cn/authing/advanced/authentication/jwt-token)）。

{% page-ref page="zhang-hao-mi-ma-ren-zheng.md" %}

{% page-ref page="shou-ji-yan-zheng-ma-ren-zheng.md" %}

{% page-ref page="xiao-cheng-xu-sao-ma-ren-zheng.md" %}

{% page-ref page="../connections/she-hui-hua-deng-lu.md" %}

## 标准协议认证

标准协议认证指通过 OAuth 2.0、OIDC、SAML、LDAP 等标准协议认证的过程，**若你的应用安全性要求较高建议使用此种认证方式**。

{% page-ref page="../connections/enterprise/pei-zhi-oauth-2.0.md" %}

{% page-ref page="../connections/enterprise/pei-zhi-oidc.md" %}

{% page-ref page="../connections/enterprise/pei-zhi-saml.md" %}

{% page-ref page="../connections/enterprise/pei-zhi-ldap.md" %}

{% page-ref page="../connections/sso.md" %}

## 你还需要了解

{% page-ref page="jwt-token.md" %}

## 如何选择认证方式

1. 若你不理解什么是 OAuth、OIDC、SAML、LDAP 等标准协议且应用安全性要求不高，建议[使用普通认证](https://learn.authing.cn/authing/advanced/authentication#pu-tong-ren-zheng)；
2. 若你的应用安全性要求较高，无论是否了解标准协议，请[使用标准协议认证](https://learn.authing.cn/authing/advanced/authentication#biao-zhun-xie-yi-ren-zheng)。





