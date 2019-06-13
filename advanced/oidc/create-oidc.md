# 创建 OIDC 应用

## 创建 OIDC 应用

在 Authing 创建 OIDC 应用非常简单，按照下图即可完成创建：

![](../../.gitbook/assets/image%20%28183%29.png)

![](../../.gitbook/assets/image%20%28146%29.png)

### 参数解释

这些参数解释假设你有 OIDC 的背景知识，如果你还不拥有背景知识，请[参考这篇文章了解 OIDC 的授权流程](https://www.cnblogs.com/linianhui/archive/2017/05/30/openid-connect-core.html)。

1. **应用 Logo**，非必填，用户登录时会在登录窗口中看到；
2. **应用名称**，必填，用户登录时会在登录窗口中看到；
3. **认证地址**，必填，一个 authing.cn 的二级域名，用户将访问此网址进行登录；
4. **回调 URL**，必填，回调到开发者自己业务的地址，在 OIDC 中必须使用 HTTPS，并且不可以为 localhost。这个值可以为多个，如需要多个，请用英文状态下的分号分割；
5. **授权模式**，必填，该 OAuth 应用支持的授权模式；
6. **返回类型**，必填，至少选择一个，界定 OIDC 支持返回的类型，不同返回类型的区别请参考：[不同返回类型对应的授权流程](https://doc.authing.cn/#/OIDCProvider/OIDCFeatures?id=不同-response_type-对应的授权流程)；
7. **Token 换取方式**，必填，默认 client\_secret\_post，若无特殊要求使用默认选项即可；
8. **id\_token 签名算法**，必填，默认 HS256，若无特殊要求使用默认选项即可。默认选项可以使用 OIDC 应用的 secret 验证 id\_token 的合法性；
9. **应用描述**，非必填，介绍下这个应用是做什么，用来做备忘；

如：

![](../../.gitbook/assets/image%20%28166%29.png)

点击确定创建完成后可看到：

![](../../.gitbook/assets/image%20%28188%29.png)

每一个 OIDC 应用都有一个 **App ID** 和一个 **App Secret**，**请妥善保管好 App Secret**。

![](../../.gitbook/assets/image%20%2843%29.png)

## 接下来你可能需要

{% page-ref page="oidc-authorization.md" %}

