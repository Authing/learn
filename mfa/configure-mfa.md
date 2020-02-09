---
description: >-
  多因素身份验证（MFA）是一种安全系统，是为了验证一项操作合法性而实行多种身份验证。例如银行的 U
  盾，异地登录要求手机短信验证。本文要介绍的是一种随时间动态变化的 6 位数字口令多因素认证。
---

# 配置 MFA 安全口令

### 准备工作

1. [注册一个 Authing 账号](https://authing.cn/login)并[创建一个 OIDC 应用](https://docs.authing.cn/authing/advanced/oidc/create-oidc)。
2. 进入 OIDC 登录页面 `https://<appDomain>.authing.cn/login/profile`

### 操作流程

输入以上网址后，由于初次访问，用户没有登录，会跳转到登录页面要求用户登录。

![&#x767B;&#x5F55;&#x4E2A;&#x4EBA;&#x4E2D;&#x5FC3;](../.gitbook/assets/image%20%2864%29.png)

{% hint style="info" %}
如果没有账户，点击「注册」选项卡，填写资料进行注册即可。
{% endhint %}

完成登录后，会跳转到个人资料中心页面。点击最右侧的「令牌设置」选项卡，打开「开启动态令牌」开关，会弹出引导窗口。

![&#x8FDB;&#x5165;&#x4EE4;&#x724C;&#x8BBE;&#x7F6E;&#x9009;&#x9879;&#x5361;](../.gitbook/assets/image%20%28265%29.png)

第一个二维码是「小登录」微信小程序的二维码，第二个二维码是 MFA 令牌的二维码。

根据提示完成令牌导入。推荐使用「小登录」微信小程序 -&gt; 左上角菜单 -&gt; 扫码添加动态令牌扫码导入。你也可以使用你喜欢的令牌管理工具，扫描 MFA 令牌二维码进行导入。

![&#x300C;&#x5C0F;&#x767B;&#x5F55;&#x300D;&#x5C0F;&#x7A0B;&#x5E8F;&#x5DE6;&#x4FA7;&#x62BD;&#x5C49;&#x83DC;&#x5355;](../.gitbook/assets/image%20%2820%29.png)

![&#x300C;&#x5C0F;&#x767B;&#x5F55;&#x300D;&#x5C0F;&#x7A0B;&#x5E8F; -&amp;gt; &#x6211;&#x7684;&#x4EE4;&#x724C;](../.gitbook/assets/image%20%2810%29.png)

导入 MFA 令牌之后，最后需要输入 6 位动态口令确认安全，完成开启。

![&#x8F93;&#x5165; 6 &#x4F4D;&#x52A8;&#x6001;&#x53E3;&#x4EE4;&#xFF0C;&#x786E;&#x8BA4;&#x5F00;&#x542F;](../.gitbook/assets/image%20%2825%29.png)

![&#x6210;&#x529F;&#x5F00;&#x542F; MFA](../.gitbook/assets/image%20%2848%29.png)

{% hint style="warning" %}
部分令牌管理工具不支持中文备注名称（如身份宝）。
{% endhint %}

接下来，点击左上角的返回按钮回到登录页面，输入你刚才开启 MFA 验证的账户的邮箱和密码，点击「登录」。

![&#x4F7F;&#x7528;&#x5F00;&#x542F;&#x8FC7; MFA &#x7684;&#x7528;&#x6237;&#x767B;&#x5F55;](../.gitbook/assets/image%20%28329%29.png)

此时，由于账户开启了 MFA 验证，会显示要求输入 MFA 口令的页面。

![](../.gitbook/assets/image%20%28320%29.png)

输入「小登录」微信小程序 -&gt; 令牌管理工具中刚刚导入的动态口令。点击「提交」。

口令核验正确后，进入了确权页面。

![](../.gitbook/assets/image%20%28227%29.png)

### 在线体验

[https://first-oidc-app.authing.cn/login/profile](https://first-oidc-app.authing.cn/login/profile)

