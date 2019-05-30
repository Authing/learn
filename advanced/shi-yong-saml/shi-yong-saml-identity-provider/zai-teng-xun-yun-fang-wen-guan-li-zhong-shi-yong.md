# 在腾讯云访问管理中使用

Authing SAML IdP 为企业用户提供SSO单点登录能力，通过 IdP 身份验证的外部用户可直接访问您的腾讯云资源。

腾讯云支持基于 [SAML 2.0 \(Security Assertion Markup Language 2.0\)](https://wiki.oasis-open.org/security?spm=a2c4g.11186623.2.14.2d7838ddUNaoo9) 的联合身份验证来实现与企业内网账号的互通。

## 在 Authing 进行配置

先在 Authing 新建一个 SAML Identity Provider 应用。

进入应用控制台 -&gt; 第三方登录 -&gt; SAML IdP 选项卡，点击右侧的「创建 SAML IdP」

在弹出的窗口中填入应用信息，填写一个二级域名作为 SAML IdP 的云上地址，点击「确定」

在 AssertionConsumeServiceURL 中输入腾讯云的 SAML 断言消费地址`https://cloud.tencent.com/login/saml`

从腾讯云[下载](https://cloud.tencent.com/saml.xml) SAML SP Metadata，并上传

在自定义 Attribute 输入以下内容，其中 `<AccountID>` 是你的腾讯云账号 ID，`<RoleName>` 是你在腾讯云创建的角色名称，`<ProviderName>` 是你在腾讯云创建的身份提供商名称。

```javascript
{
    "https://cloud.tencent.com/SAML/Attributes/Role": "qcs::cam::uin/<AccountID>:roleName/<RoleName>,qcs::cam::uin/<AccountID>:saml-provider/<ProviderName>",
    "https://cloud.tencent.com/SAML/Attributes/RoleSessionName": "Test"
}
```

打开对 SAML Response 签名开关，上传或输入一对秘钥，并正确选择秘钥算法

点击「确定」

在使用方法页面点击「下载」IdP Metadata

## 在腾讯云控制台进行配置

从腾讯云进入用户昵称触发下拉菜单 -&gt; 访问管理页面 -&gt; 左侧导航栏选择身份提供商，点击蓝色按钮「新建身份提供商」

此处需要上传刚才下载的 IdP Metadata XML 文件。提供商名称需要填入刚才在 Authing 自定义 Attribute 中填写的的`<ProviderName>`。

![](../../../.gitbook/assets/image%20%28112%29.png)

上传后点击下一步。

在审阅页面点击「完成」

![&#x786E;&#x8BA4;&#x4FE1;&#x606F;](../../../.gitbook/assets/image%20%2855%29.png)

从腾讯云进入用户昵称触发下拉菜单 -&gt; 访问管理页面 -&gt; 左侧导航栏选择角色，点击蓝色按钮「新建角色」，在弹出的窗口中选择身份提供商。

![&#x65B0;&#x5EFA;&#x89D2;&#x8272;](../../../.gitbook/assets/image%20%289%29.png)

选择刚才创建的身份提供商 Authing

![&#x9009;&#x62E9;&#x8EAB;&#x4EFD;&#x63D0;&#x4F9B;&#x5546;](../../../.gitbook/assets/image%20%2825%29.png)

点击「下一步」

在角色权限配置页面选择授予此身份提供商的权限，这里选择 Administrator 权限，即全权访问。

![&#x89D2;&#x8272;&#x7B56;&#x7565;](../../../.gitbook/assets/image%20%2890%29.png)

点击「下一步」

在审阅页面，输入角色名称，需要和之前在 Authing 填写的 `<RoleName>` 一致。

![](../../../.gitbook/assets/image%20%2885%29.png)

点击下方「完成」

## 使用 IdP 登录腾讯云

从腾讯云进入用户昵称触发下拉菜单 -&gt; 访问管理页面 -&gt; 左侧导航栏选择身份提供商

点击刚刚创建的身份提供商

![&#x67E5;&#x770B;&#x8BE6;&#x60C5;](../../../.gitbook/assets/image%20%2823%29.png)

复制登录链接，到浏览器新标签页进行访问。

![&#x590D;&#x5236;&#x767B;&#x5F55;&#x94FE;&#x63A5;](../../../.gitbook/assets/image%20%2822%29.png)

点击「确定跳转」

![&#x4ECE; IdP &#x767B;&#x5F55;&#x817E;&#x8BAF;&#x4E91;](../../../.gitbook/assets/image%20%2849%29.png)

浏览器被重定向到 Authing IdP 用户登录认证页，选择一种登录方式，输入信息。点击「登录」

在确权页面点击「授权登录」

浏览器会跳转到腾讯云控制台，单点登录成功

![&#x5355;&#x70B9;&#x767B;&#x5F55;&#x6210;&#x529F;](../../../.gitbook/assets/image%20%2884%29.png)

