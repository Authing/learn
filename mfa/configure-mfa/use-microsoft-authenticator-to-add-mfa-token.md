# 使用 Microsoft Authenticator 添加 MFA 令牌

## 准备工作

1. [注册一个 Authing 账号](https://authing.cn/login)并[创建一个 OIDC 应用](https://docs.authing.cn/authing/advanced/oidc/create-oidc)。
2. 进入 OIDC 登录页面 `https://<appDomain>.authing.cn/login/profile`

你可以在此在线体验：[https://first-oidc-app.authing.cn/login/profile](https://first-oidc-app.authing.cn/login/profile)

## 开启账户 MFA 令牌 <a id="turn-on-mfa"></a>

输入以上网址后，由于初次访问，用户没有登录，会跳转到登录页面要求用户登录。

![&#x767B;&#x5F55;&#x4E2A;&#x4EBA;&#x4E2D;&#x5FC3;](../../.gitbook/assets/image%20%28104%29.png)

{% hint style="info" %}
如果没有账户，点击「注册」选项卡，填写资料进行注册即可。
{% endhint %}

完成登录后，会跳转到个人资料中心页面。点击最右侧的「令牌设置」选项卡，打开「开启动态令牌」开关，会弹出引导窗口。

![&#x8FDB;&#x5165;&#x4EE4;&#x724C;&#x8BBE;&#x7F6E;&#x9009;&#x9879;&#x5361;](../../.gitbook/assets/image%20%28406%29.png)

第一个二维码是「小登录」微信小程序的二维码，这里不用理会，**直接点击下一步**。

![](https://cdn.authing.cn/docs/20200401112446.png)

第二个二维码是 MFA 令牌二维码。

![](https://cdn.authing.cn/docs/20200401112537.png)

请在手机上打开「Microsoft Authenticator」应用，然后在右上角的三点菜单，选择「添加账户」。

![](https://cdn.authing.cn/docs/20200401113547.png)

在新弹出的页面，选择「其他账户」。

![](https://cdn.authing.cn/docs/20200401114204.png)

扫码添加 MFA 令牌。

![](https://cdn.authing.cn/docs/Lark20200401-114407.jpeg)

之后可以在界面上看到刚刚添加的 MFA 令牌条目。

![](https://cdn.authing.cn/docs/20200401115040.png)

将显示的密钥输入到网页对话框。

![&#x786E;&#x8BA4;&#x53E3;&#x4EE4;](https://cdn.authing.cn/docs/20200401115155.png)

然后点击「确定」。此时 MFA 令牌已开启成功。

![MFA &#x5F00;&#x542F;&#x6210;&#x529F;](https://cdn.authing.cn/docs/20200401115317.png)

## 使用 MFA 令牌登录 <a id="login-with-mfa"></a>

接下来，点击左上角的返回按钮回到登录页面。

![](https://cdn.authing.cn/docs/20200401115816.png)

输入你刚才开启 MFA 验证的账户的邮箱和密码，点击「登录」。

![&#x4F7F;&#x7528;&#x5F00;&#x542F;&#x8FC7; MFA &#x7684;&#x7528;&#x6237;&#x767B;&#x5F55;](../../.gitbook/assets/image%20%28514%29.png)

此时，由于账户开启了 MFA 验证，会显示要求输入 MFA 口令的页面。

![](../../.gitbook/assets/image%20%28504%29.png)

输入「Microsoft Authenticator」中刚刚导入的动态口令。点击「提交」。

口令核验正确后，进入了确权页面。

![](../../.gitbook/assets/image%20%28355%29.png)

### 





