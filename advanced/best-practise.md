# 最佳开发实践

## 使用 OIDC 协议对用户进行认证和授权

{% page-ref page="oidc/" %}

## 使用 Guard

{% hint style="info" %}
若你不想（或不懂）使用 OIDC 协议，同时你在开发 Web 应用，那么建议使用 Guard 对用户进行认证。
{% endhint %}

{% page-ref page="../sdk/guard/" %}

同时我们建议你配置社会化登录或小程序扫码登录（终端用户拥有更好的登录体验）：

{% page-ref page="social-login/" %}

{% page-ref page="wxapp-qrcode/" %}

## 在后端验证 JWT Token 的合法性

点击[查看如何验证 JWT Token 的合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。

{% hint style="info" %}
若你使用了 OIDC 流程，建议在后端验证 JWT Token 的合法性（避免 OIDC 密钥泄漏）。
{% endhint %}





