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

{% page-ref page="../advanced/oidc/" %}

## 使用 Guard

{% hint style="info" %}
若你不想（或不懂）使用 OIDC 协议，那么建议使用 Guard 对用户进行认证。
{% endhint %}

{% page-ref page="../sdk/guard/" %}

{% page-ref page="../sdk/guard-for-mobile/" %}

同时我们建议你配置社会化登录或小程序扫码登录使得终端用户拥有更好的登录体验：

{% page-ref page="../advanced/social-login/" %}

{% page-ref page="../scan-qrcode/wxapp-qrcode/" %}

## 在后端验证 JWT Token 的合法性

点击[查看如何验证 JWT Token 的合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。

{% hint style="info" %}
若你使用了 OIDC 流程，建议在后端验证 JWT Token 的合法性（避免 OIDC 密钥泄漏）。
{% endhint %}

## 在后端将 Authing 用户 ID 与业务数据做连接

使用 Authing 后不用建立单独的用户表，若你想拓展用户字段，建议将 Authing 用户 ID 与拓展的用户字段连接，若有其他业务数据，请将 Authing 用户 ID 与业务数据做连接。

Authing 的用户 ID 在返回结果中的字段名称为「\_id」。

