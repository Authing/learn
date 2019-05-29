# 同时使用 Authing 作为 SP 和 IdP

本教程讲述如何使用 Authing 云上的 SAML SP 和 SAML IdP 为企业或个人快速接入单点认证能力。完成本教程后，你会拥有运行在 Authing 云上的 SAML Service Provider 和 SAML Identity Provider 来处理 SAML 协议相关的过程，而你只需关注 Authing 返回的用户信息与 JWT Token。

在用户访问受保护的资源时，结合 Authing SDK 可以对他携带的 JWT Token 进行验证。如果 JWT Token 验证失败，只需将用户重定向到 SAML Service Provider 的云上地址，它会负责通过 SAML 协议与 IdP 交换信息从而认证用户，并返回 JSON 格式的用户信息。

## 创建一个 SAML IdP 应用

## 创建一个 SAML SP 应用

## 开始使用

