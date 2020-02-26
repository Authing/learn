---
description: 社会化登录指：微信登录、支付宝登录、钉钉登录等。
---

# 在云上配置社会化登录

{% hint style="info" %}
如果你使用了 Authing 的二级域名处理用户认证，请参考本文配置社会化登录。
{% endhint %}

在[注册 Authing 账号](https://authing.cn)之后，我们会要求你填写一个二级域名用来为终端用户提供认证服务，如果你使用这种默认方式提供服务，不用写任何代码即可完成社会化登录接入。

## 创建 Github OAuth 应用

请点击下面这个卡片，按照官方的说明流程创建一个 Github OAuth 应用：

{% embed url="https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/" %}

{% hint style="info" %}
**重要提示，在创建 Github OAuth 应用时填写的 Callback URI 请填写为：**

\*\*\*\*[https://oauth.authing.cn/oauth/github/redirect](https://oauth.authing.cn/oauth/github/redirect)

如下图所示：

![](../../.gitbook/assets/image%20%28271%29.png)
{% endhint %}

## 在 Authing 控制台配置 Github OAuth 信息

在 Github 创建完 OAuth 应用后请按照下图所示在 Authing 控制台中配置 Github OAuth 信息：

![](../../.gitbook/assets/image%20%28136%29.png)

打开开关后将弹出如下信息框：

![](../../.gitbook/assets/image%20%28113%29.png)

相关参数解释如下：

| Client ID | Client Secret | Redirect |
| :--- | :--- | :--- |
| 在 OAuth 平台上申请的 APP ID 或 Client ID | OAuth 平台提供的 Client Secret 或 App Secret | 成功或失败后的回调 URL（你的业务地址） |

![](../../.gitbook/assets/image%20%28341%29.png)

配置完成后请点击「确定」保存信息。

{% hint style="info" %}
如果你没有业务地址，可以以「\#」代替，此处不影响使用。
{% endhint %}

## 完成配置

在控制台中配置完成后就可以访问你的二级域名查看新的社会化登录选项了。

在使用社会化登录登录完成后不会跳到你指定的业务地址，而是会由 Authing 接管接并写入到用户池中。

获取用户的用户信息和 Token 请参考「实现单点登录」和相应的 AuthingSSO SDK：

{% page-ref page="../../quickstart/implement-sso-with-authing.md" %}

{% embed url="https://github.com/Authing/AuthingSSO" %}



