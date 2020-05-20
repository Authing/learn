# 创建 OAuth 应用

## 创建 OAuth 应用

在 Authing 创建 OAuth 应用非常简单，按照下图即可完成创建：

![](../../.gitbook/assets/image%20%28261%29.png)

![](../../.gitbook/assets/image%20%28428%29.png)

### 参数解释

1. **应用 Logo**，非必填，用户会在登录页面看到此 Logo；
2. **应用名称**，必填，用户会在登录页面看到此应用名称；
3. **认证地址**，必填，一个 authing.cn 的二级域名，用户将访问此网址进行登录；
4. **回调 URL**，必填，回调到开发者自己业务的地址；
5. **授权模式**，必填，该 OAuth 应用支持的授权模式；
6. **应用描述**，非必填，介绍下这个应用是做什么，用来做备忘；

如：

![](../../.gitbook/assets/image%20%2867%29.png)

点击确定创建完成后可看到：

![](../../.gitbook/assets/image%20%28615%29.png)

每一个 OAuth 应用都有一个 **App ID** 和一个 **App Secret**，**请妥善保管好 App Secret**。

![](../../.gitbook/assets/image%20%28270%29.png)

此时访问 [https://first-oauth-app.authing.cn](https://first-oauth-app.authing.cn) 可以看到如下界面：

![](../../.gitbook/assets/image%20%28597%29.png)

用户将通过此界面进入开发者接入的其他应用。

## 接下来你可能需要

{% page-ref page="oauth-authorization.md" %}

