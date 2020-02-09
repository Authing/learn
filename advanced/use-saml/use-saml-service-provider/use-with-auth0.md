# 与 Auth0 SAML IdP 对接

## 在 Auth0 创建 SAML IdP

请查看 Auth0 文档 [https://auth0.com/docs/protocols/saml/saml-idp-generic](https://auth0.com/docs/protocols/saml/saml-idp-generic)

## 在 Authing 配置 SP

创建一个 Authing SP。

进入应用控制台 -&gt; 第三方登录 -&gt; 社会化登录，在 SAML Service Provider 区域，点击「+」。

填写应用信息，为云上的 SP 指定一个二级域名，填写用户信息回调地址，点击「确定」。

![](../../../.gitbook/assets/image%20%2893%29.png)

在 IdP 登录 URL 填入 Auth0 IdP 的登录地址，形如`https://YOUR_DOMAIN/samlp/CLIENT_ID`

Auth0 IdP 登出 URL 形如`https://YOUR_DOMAIN/samlp/CLIENT_ID/logout`。

entityID 填入 Auth0 IdP 的 Issuer，形如 `urn:xxx.auth0.com`。

上传 Auth0 的 IdP Metadata。

![&#x586B;&#x5199; SAML IdP &#x57FA;&#x672C;&#x4FE1;&#x606F;&#x5E76;&#x4E0A;&#x4F20; IdP Metadata](../../../.gitbook/assets/image%20%2897%29.png)

在下方 SAML Response 验签证书处上传在 Auth0 下载的 IdP 证书。

![&#x4E0A;&#x4F20; auth0 IdP &#x7684;&#x8BC1;&#x4E66;](../../../.gitbook/assets/image%20%28177%29.png)

其他选项保留默认即可。

点击「确定」。

在字段映射设置中选择 Auth0，点击确定。

![&#x914D;&#x7F6E;&#x5B57;&#x6BB5;&#x6620;&#x5C04;](../../../.gitbook/assets/image%20%28173%29.png)

## 在 Auth0 填入 Authing SAML SP ACS 地址

在 Applications -&gt; 应用列表条目 -&gt; Addons -&gt; SAML2 WEB APP -&gt; Allowed Callback URL 中填入 Authing SP 的 AssertionConsumeURL。

![&#x586B;&#x5165; Authing SP &#x7684; ACS &#x5730;&#x5740;](../../../.gitbook/assets/image%20%2840%29.png)

## 使用 Authing SAML SP 发起登录

将 Authing SAML SP 应用详情 -&gt; 使用方法选项卡 -&gt; 发起 SAML 登录项目下的地址复制到浏览器地址栏，进行访问。

![](../../../.gitbook/assets/image%20%28104%29.png)

浏览器会跳转到 Auth0 IdP 用户认证登录页，选择一种方式进行登录。

![](../../../.gitbook/assets/image%20%28211%29.png)

浏览器会跳转到在 Authing SP 设置的回调地址，以 url query 的形式附带用户信息，key 为 data。

![](../../../.gitbook/assets/image%20%28322%29.png)

