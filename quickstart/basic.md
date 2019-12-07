---
description: 学习 Authing 的基础知识以及我们会用到的一些术语。
---

# 基础概念

在科技领域，学习新知识最大的障碍就来自于「术语」，当你试图去了解一个新概念时，大量的术语将会对你的理解造成阻碍。本文将会解释 Authing 中的常用术语，以期能帮助你尽快熟悉在 Authing 中涉及到的知识。

{% hint style="success" %}
本文将介绍**用户池、账户、通信、域名和第三方登录**等概念，如果你对这些概念比较熟悉，可以放心跳过本章。

我们会假设有一家名为「非凡」的科技公司，其公司人数有 300 多人，这家公司想使用 Authing 对他们的 Web 应用进行用户名密码和微信认证，同时希望能管理自己内部的软件系统，实现员工对内部软件的单点登录。
{% endhint %}

## 用户池

[注册完 Authing 账户](https://authing.cn/login)后，你看到的是一个空白的列表页，此时你需要创建一个**用户池**。拥有了一个用户池之后你可以授权其他应用（第三方应用或者你自己写的应用）读取用户数据，授权协议支持 [OAuth2.0](https://docs.authing.cn/authing/advanced/oauth2)、[OIDC](https://docs.authing.cn/authing/advanced/oidc)、[SAML](https://docs.authing.cn/authing/advanced/shi-yong-saml) 和 [LDAP](https://docs.authing.cn/authing/advanced/ldap)。

![&#x70B9;&#x51FB;&#x300C;&#x521B;&#x5EFA;&#x7528;&#x6237;&#x6C60;&#x300D;&#x5373;&#x53EF;&#x4EE5;&#x521B;&#x5EFA;](../.gitbook/assets/image%20%2892%29.png)

![&#x586B;&#x5199;&#x7528;&#x6237;&#x6C60;&#x540D;&#x79F0;&#x548C;&#x7528;&#x6237;&#x6C60;&#x7C7B;&#x578B;](../.gitbook/assets/image%20%28201%29.png)

创建应用时可以选择如下四种类型：

![&#x53EF;&#x4F9B;&#x9009;&#x62E9;&#x7684;&#x56DB;&#x79CD; Authing &#x5E94;&#x7528;&#x7C7B;&#x578B;&#xFF0C;&#x6839;&#x636E;&#x5B9E;&#x9645;&#x60C5;&#x51B5;&#x586B;&#x5199;&#x5373;&#x53EF;](../.gitbook/assets/image%20%281%29.png)

每一个用户池都有一个唯一的 **clientId**，比如「5cc59bdf1bbaf0188cce6d84」，这个 clientId 是不可修改的，主要用来标识你的用户池；除了 clientId ，每一个用户池还有一个 **secret**，**clientId** 和 **secret** 可以在代码层面授权开发者访问用户池内的用户数据（包含注册、登录、退出、修改资料、删除用户等操作）。

clientId 和 secret 在控制台中的查看位置如下图所示：

![&#x5728;&#x5E94;&#x7528;&#x7684;&#x300C;&#x8BBE;&#x7F6E;&#x300D;&#x83DC;&#x5355;&#x4E2D;&#x53EF;&#x4EE5;&#x67E5;&#x770B; clientId &#x548C; secret](../.gitbook/assets/image%20%2828%29.png)

如上图所示，如果你的 secret 不小心泄漏，可以通过「刷新」按钮重置 secret。但是请注意，如果你在某些程序中使用了该 secret，那么使用该 secret 的程序也必须要手动更新 secret，否则会造成该程序的「身份认证功能」毁坏。

{% hint style="success" %}
在本文的例子中，「非凡科技」有一个 Web 应用，所以他选择的应用类型为「Web」，并为此应用取名为「非凡科技用户池」。
{% endhint %}

如果你有很多独立的系统，那么可以创建多个「用户池」来分割用户，如果你想多个应用共用一个用户池，请先学习我们的[单点登录](https://learn.authing.cn/authing/quickstart/basic#dan-dian-deng-lu)概念。

## 账号

账号是你创建的用户池中的单个用户。每一个账号都有一个唯一的 \__id，_你在使用 Authing 时可以将此 \_id 存入你自己的数据库中，然后与自己的具体业务进行联表。

单个账号可以使用四种基础方式进行登录：

1. 邮箱 - 密码登录
2. 手机 - 验证码登录
3. 小程序扫码登录
4. 社会化网络登录（微信登录、Github 登录等）

你可以在控制台中创建用户：

![&#x652F;&#x6301;&#x4F7F;&#x7528;&#x90AE;&#x7BB1;&#x548C;&#x624B;&#x673A;&#x521B;&#x5EFA;](../.gitbook/assets/image%20%2832%29.png)

{% hint style="success" %}
在本文的例子中，「非凡科技」创建了一个邮箱为「test@feifantech.com」的账号用来测试 Authing 的账号系统。
{% endhint %}

![&#x5728;&#x63A7;&#x5236;&#x53F0;&#x4E2D;&#x53EF;&#x4EE5;&#x770B;&#x5230;&#x7684;&#x7528;&#x6237;&#x8D44;&#x6599;](../.gitbook/assets/image%20%28227%29.png)

在「原始 JSON 数据」选项卡中可以看到一段用来表示该账号的 JSON 数据：

```javascript
{
	"_id": "5ccbf7551bbaf0f9edced9fd",
	"email": "test@feifantech.com",
	"phone": "",
	"emailVerified": false,
	"username": "test",
	"nickname": "",
	"unionid": null,
	"company": "",
	"photo": "https://usercontents.authing.cn/authing-avatar.png",
	"browser": "",
	"registerInClient": "5ccbf4d61bbaf07aefced9d5",
	"registerMethod": "default:username-password",
	"oauth": "",
	"token": null,
	"tokenExpiredAt": null,
	"loginsCount": 0,
	"lastLogin": "Fri May 03 2019 16:09:57 GMT+0800 (CST)",
	"lastIP": null,
	"signedUp": "Fri May 03 2019 16:09:57 GMT+0800 (CST)",
	"blocked": false,
	"isDeleted": false,
	"clientType": null,
	"userLocation": [],
	"userLoginHistory": {
		"totalCount": 0,
		"list": []
	},
	"systemApplicationType": null
}
```

### 用户组和权限

Authing 中可以配置每个账号所属的用户组和用户组权限，如果你想深入使用用户组和权限请参考[用户权限](https://learn.authing.cn/authing/advanced/roles)一章。

## 消息

消息是 Authing 提供的**邮件发送**和**短信发送**能力。

在 Authing 中，使用邮箱注册的用户在注册成功后会收到两封邮件，一封是「欢迎邮件」，另一封是「用来验证邮箱是属于注册者自己的验证邮件」，这由 Authing 的邮件服务器进行发送。

如果你想修改邮件模版，可以选择「邮件模版」进行配置。

如果你想邮件发送服务器，可以选择「第三方邮件服务」进行配置。

此外，如果用户使用手机 - 验证码登录，那么其背后的短信发送也由 Authing 完成，Authing 的短信发送能力依赖于第三方短信服务。

![&#x6D88;&#x606F;&#x670D;&#x52A1;&#x5728;&#x63A7;&#x5236;&#x53F0;&#x4E2D;&#x7684;&#x4F4D;&#x7F6E;](../.gitbook/assets/image%20%28316%29.png)

{% hint style="success" %}
在本文的例子中，「非凡科技」修改了邮件模版，将邮件主题「你已成功注册 {{ client\_name }}」修改为了「欢迎注册 {{ client\_name }}」。
{% endhint %}

Authing 的邮件模版支持宏命令，可以包括以下宏：

1. {{ client\_name }}：邮件正文中的 {{ client\_name }} 会被替换成当前应用的名称；
2. {{ time }}：邮件正文中的 {{ time }} 会被替换为当前时间；
3. {{ client\_description }}：邮件正文中的 {{ client\_description }} 会被替换成当前应用的描述（在应用配置中可进行修改）；
4. {{ user\_email }}：邮件正文中的 {{ user\_email }} 会被替换成当前注册用户的邮箱。

在目前的 Authing 版本中，**不允许修改短信模版**。

## 第三方登录

第三方登录指用户使用第三方应用登录自己的应用，简单来说，是指基于用户在第三方平台上已有的账号和密码来快速完成己方应用的登录或者注册的功能。常见的第三方登录平台，一般是已经拥有大量用户的平台，国内大体为：微信、微博、QQ 等，国外有 Facebook、Twitter、Google 等。

除了对 C 端用户的便捷外，还有对企业用户的方便。每家现代企业都或多或少会用到一些 SaaS 系统，如邮箱、代码托管、ERP、HR 类软件等，为了让员工使用一个账号即可访问所有的系统，单点登录便成为了一个亟待解决的需求。大型公司会自研系统，中小型公司多会寻求第三方解决方案。

**Authing 将此类身份提供商统称为「第三方登录」。**

### 社会化登录

社会化登录指使用微信、Github 此类平台登录其他应用。目前 Authing 支持使用微信和 Github 登录，此外还支持扫小程序码登录。Authing 将此类功能抽象化，使开发者不用关心具体的实现，只需要在控制台中配置即可完成。

### 单点登录

单点登录（SSO）的目的是让用户只登录一次，然后就可以登录所有接入该 SSO 的系统。同样的，当注销用户登录的每个应用程序或服务会话时，会发生统一注销。

对于使用了 SSO 的应用程序，一般来说拥有三个不同的会话层：

1. 应用程序维护的本地会话
2. 授权服务器会话
3. 身份提供者会话，如果用户选择通过身份提供商（例如 Google，Facebook 或企业 SAML 身份提供商）登录

[点击这里了解如何在 Authing 中使用 SSO。](https://learn.authing.cn/authing/advanced/connections/sso)

{% hint style="success" %}
在本文的例子中，「非凡科技」希望他们的用户可以使用邮箱 - 验证码、小程序扫码以及 Github 进行登录，所以具体的操作步骤为：
{% endhint %}

1. 配置 Github 的第三方登录；
2. 配置小程序扫码；
3. 使用 Guard 挂载到自己的应用程序中；

## 域名

Authing 提供自定义二级域名（\*.authing.cn）的能力，这个二级域名是用户进行身份认证的地址（仅限[单点登录](https://learn.authing.cn/authing/quickstart/basic#dan-dian-deng-lu)）。

在你创建 OAuth 应用、 OIDC 应用和 SAML 应用时会要求你填写你想要使用的二级域名，同时我们有一些保留域名，保留域名列表请参考下表：

| 保留域名名称 | 理由 |
| :--- | :--- |
| users | 内部域名 |
| oauth | 内部域名 |
| client | 内部域名 |
| dashboard | 内部域名 |
| fuck | 非法域名 |
| shit | 非法域名 |
| bitch | 非法域名 |

同时，Authing 有权对用户自定义的域名名称进行审核，若发现违法违规的域名，Authing 有权对其关闭和上报公安机关。

## 接下来你可能还需要

使用 Authing 实现单点登录：

{% page-ref page="implement-sso-with-authing.md" %}

控制台是你管理所有 Authing 资源的地方，了解 Authing 控制台各模块包含的内容和你可以在控制台中做的事情：

{% page-ref page="dashboard.md" %}

了解 Authing 提供的多种部署模型，以帮助你选择该以怎样的形式部署 Authing：

{% page-ref page="deployment.md" %}

