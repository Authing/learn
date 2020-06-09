# 最佳开发实践

## 将 Authing 作为身份中台来使用

1. 将你的所有用户存储在 Authing 平台，不论是员工数据还是自研应用的用户数据，抑或是 IoT 设备数据；
2. 从 Authing 中读取/修改用户资料和角色权限；
3. 经由 Authing 判断用户/设备的登录状态；

### 参考资料

{% page-ref page="implement-sso-with-authing.md" %}

{% page-ref page="../sdk/sdk-for-node/org-structure.md" %}

{% page-ref page="../advanced/verify-jwt-token.md" %}

## 在 Web 应用中使用单点登录实现用户认证和登录状态检查

{% page-ref page="implement-sso-with-authing.md" %}

## 使用 OIDC 协议对用户进行认证和授权

{% page-ref page="../authentication/oidc/" %}

## 使用 Guard

{% hint style="info" %}
若你不想（或不懂）使用 OIDC 协议，那么建议使用 Guard 对用户进行认证。
{% endhint %}

{% page-ref page="../sdk/guard/" %}

{% page-ref page="../sdk/guard-for-mobile/" %}

同时我们建议你配置社会化登录或小程序扫码登录使得终端用户拥有更好的登录体验：

{% page-ref page="../social-login/web/" %}

{% page-ref page="../social-login/mobile/" %}

{% page-ref page="../scan-qrcode/wxapp-qrcode/" %}

## 在后端验证 JWT Token 的合法性

点击[查看如何验证 JWT Token 的合法性](https://docs.authing.co/authing/advanced/verify-jwt-token#oidc-secret-token)。

{% hint style="info" %}
若你使用了 OIDC 流程，建议在后端验证 JWT Token 的合法性（避免 OIDC 密钥泄漏）。
{% endhint %}

## 在后端将 Authing 用户 ID 与业务数据做连接

使用 Authing 后不用建立单独的用户表，若你想拓展用户字段，建议将 Authing 用户 ID 与拓展的用户字段连接，若有其他业务数据，请将 Authing 用户 ID 与业务数据做连接。

Authing 的用户 ID 在返回结果中的字段名称为「\_id」。

## 使用云开发执行后续业务逻辑

腾讯云云开发（CloudBase）是一款云端一体化的产品方案 ，采用 Serverless 架构，提供云函数、云数据库、云存储等基础资源服务，免环境搭建等运维事务 ，同时提供静态托管、命令行工具（CLI）、Flutter SDK 等能力，极大降低应用开发门槛，助力快速构建小程序、Web 应用和 App。

在使用 Authing 完成认证后可以非常方便的使用云开发完成后续业务逻辑，请参考以下文档：

{% embed url="https://cloud.tencent.com/product/tcb/details" %}

