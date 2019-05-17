---
description: 使用微信、Github 等社会化登录。
---

# 使用社会化登录

社会化登录指使用微信、Github 等互联网服务登录自己的服务。

本章节将以接入 Github 为例，演示如何使用 Authing 快速接入社会化登录，其他社会化登录同理。

## 创建 Github OAuth 应用

请点击下面这个卡片，按照官方的说明流程创建一个 Github OAuth 应用：

{% embed url="https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/" %}

{% hint style="info" %}
**重要提示，在创建 Github OAuth 应用时填写的 Callback URI 请填写为：**

\*\*\*\*[https://oauth.authing.cn/oauth/github/redirect](https://oauth.authing.cn/oauth/github/redirect)
{% endhint %}

## 在 Authing 控制台配置 Github OAuth 信息

创建完成 OAuth 应用后请按照下图所示开始配置 Github OAuth 信息：

![](../.gitbook/assets/image%20%2823%29.png)

打开开关后将弹出如下信息框：

![](../.gitbook/assets/image%20%2817%29.png)

相关参数解释如下：

| Client ID | Client Secret | Redirect |
| :--- | :--- | :--- |
| 在 OAuth 平台上申请的 APP ID 或 Client ID | OAuth 平台提供的 Client Secret 或 App Secret | 成功或失败后的回调 URL |

配置完成后请点击「确定」保存信息。

接下来，你可以通过拼接以下 URL 执行 OAuth 登录流程：

[https://github.com/login/oauth/authorize/?client\_id=&lt;Github\_Client\_ID&gt;&state=&lt;Authing\_Client\_ID&gt;](https://github.com/login/oauth/authorize/?client_id=<Github_Client_ID>&state=<Authing_ClientId>
)

如 Authing 的官方「使用 Github 登录」链接：

[https://github.com/login/oauth/authorize/?client\_id=5446d8d6083f27fed5b4&state=59f86b4832eb28071bdd9214](https://github.com/login/oauth/authorize/?client_id=5446d8d6083f27fed5b4&state=59f86b4832eb28071bdd9214)

{% hint style="info" %}
你可以在 Web 页面上放置一个可点击的按钮或 Logo 链接到上面的链接以便用户可以点击登录。
{% endhint %}

## 获取用户数据

用户在访问上面拼接好的链接后，将回调至开发者配置好的 Redirect URL，并附带以下参数：

| 参数 | 说明 |
| :--- | :--- |
| code | 错误或成功代码，200 为成功，非 200 为失败 |
| message | 成功或错误信息 |
| data | userInfo，若 code 为非 200 不返回此参数 |
| bindOAuth | 是否是登录操作， 无此参数或此参数为`0`则为登录, 此参数为`1`则为绑定账号操作, 此时可使用`data`参数中的数据[绑定OAuth](https://docs.authing.cn/#/oauth/bind_oauth) |

## 完成接入



## 其他资源

