---
description: 如果你的微信应用使用同一微信主体注册，Authing 会将终端用户自动识别为同一个用户，不需要你进行额外操作。
---

# 打通微信账号体系

## 准备工作 <a id="prepare"></a>

你一共需要准备以下内容：

1. [注册 Authing 开发者账号](../quickstart/create-authing-account.md)
2. 使用同一个微信主体注册微信移动应用、网站应用、公众账号、小程序等。
3. 分别使用 Authing 接入微信登录：
   * [微信 PC 扫码登录](web/wechat-pc.md)（网站应用）
   * [微信 APP 内网页登录](web/wechat-mp.md)（公众账号）
   * [移动 APP 使用微信登录](mobile/wechat.md) （移动应用）
   * [小程序登录](miniprogram/miniprogram/) （小程序）
   * [移动 APP 拉起小程序登录](miniprogram/app2wxapp.md) （小程序）
   * [小程序扫码登录](../scan-qrcode/wxapp-qrcode/)（小程序）

### 如何查看同一微信主体注册的所有微信应用 <a id="wechat"></a>

前往[微信开放平台](https://open.weixin.qq.com/) - **管理中心** 页面，你可以看到该微信主体注册的所有微信应用：

![](../.gitbook/assets/image%20%28116%29.png)

## 开始接入 <a id="start"></a>

这里我们以接入[**微信 PC 扫码登录**](web/wechat-pc.md)和[**小程序扫码登录**](../scan-qrcode/wxapp-qrcode/)为例，介绍一下如何打通微信账号体系。

### 第一步：接入微信 PC 扫码登录 <a id="wechat-pc"></a>

具体接入流程请见：

{% page-ref page="web/wechat-pc.md" %}

### 第二步：接入小程序扫码登录 <a id="wechat-miniprogram"></a>

「小程序扫码登录」指的是：扫描小程序二维码登录 Web 应用，据 Authing 客户统计，该方式能提高 80% 的注册转化率并在获取到用户手机号的同时，节省 100% 的短信成本。详细介绍请见：[https://wxapp.authing.cn/](https://wxapp.authing.cn/) 。

{% hint style="success" %}
「[句子互动](https://botorange.com/)」正是采用的这种登录方式，广受用户好评。
{% endhint %}

![](../.gitbook/assets/image%20%28612%29.png)

具体接入流程请见：

{% page-ref page="../scan-qrcode/wxapp-qrcode/" %}

### 接下来：Authing 自动为你完成统一微信账号的工作 <a id="authing"></a>

如果你的微信应用使用同一微信主体注册，Authing 会将终端用户自动识别为同一个用户，不需要你进行额外操作。

下面我们来演示验证一下：

{% hint style="info" %}
下面演示中用到的用户池（[https://sample.authing.cn/](https://sample.authing.cn/)），配置的**微信 PC 扫码登录**和**小程序扫码登录**方式**微信主体**为同一个。
{% endhint %}

#### 使用 PC 扫码登录 

前往 [https://sample-sso.authing.cn/](https://sample-sso.authing.cn/) 登录页面，选择「使用微信扫码登录」：

![](../.gitbook/assets/image%20%2857%29.png)

登录之后，你在跳转页面可以看到自己的完整用户信息：

![](https://cdn.authing.cn/blog/20200410140449.png)

在此我们可以找到用户 ID：

![](https://cdn.authing.cn/blog/20200410140530.png)

这里我们演示的账号用户 ID 为：

```text
5e900be284d1594238604b8e
```

#### 使用小程序扫码登录

点击「**退出**」之后，再次点击「**体验 Web 端登录**」：

![](https://cdn.authing.cn/blog/20200410140630.png)

![](https://cdn.authing.cn/blog/20200410140706.png)

这一次我们选择使用**小程序扫码登录**：

![](https://cdn.authing.cn/blog/20200410140810.png)

登录之后再次跳转到上一页面，我们看到用户 ID 还是 5e900be284d1594238604b8e .

![](https://cdn.authing.cn/blog/20200410140856.png)

由此可见 Authing 将同一微信用户的两次「使用微信扫码登录」、「使用小程序登录」识别为了同一用户。

