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
    "https://cloud.tencent.com/SAML/Attributes/Role": "qcs::cam::uni/<AccountID>:roleName/<RoleName>,qcs::cam::uni/<AccountID>:saml-provider/<ProviderName>",
    "https://cloud.tencent.com/SAML/Attributes/RoleSessionName": "Test"
}
```

点击「确定」

## 在腾讯云控制台进行配置

从腾讯云进入访问管理页面，在左侧导航栏选择身份提供商，点击蓝色按钮「新建身份提供商」

![&#x65B0;&#x5EFA;&#x8EAB;&#x4EFD;&#x63D0;&#x4F9B;&#x5546;](../../../.gitbook/assets/image%20%2899%29.png)

此处需要上传 IdP Metadata XML 文件。

![&#x4E0A;&#x4F20;&#x5143;&#x6570;&#x636E;&#x6587;&#x6863;](../../../.gitbook/assets/image%20%2882%29.png)

上传后点击下一步。

点击确认，完成创建

![&#x5BA1;&#x9605;](../../../.gitbook/assets/image%20%28121%29.png)

