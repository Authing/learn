# 与 Auth0 SAML IdP 对接

## 在 Auth0 创建 SAML IdP

请查看 Auth0 文档 [https://auth0.com/docs/protocols/saml/saml-idp-generic](https://auth0.com/docs/protocols/saml/saml-idp-generic)



## 在 Authing 配置 SP

创建一个 Authing SP。

进入应用控制台 -&gt; 第三方登录 -&gt; 社会化登录，在 SAML Service Provider 区域，点击「+」。

填写应用信息，为云上的 SP 指定一个二级域名，点击「确定」。

在 IdP 登录 URL 填入 Auth0 IdP 的登录地址。

Auth0 IdP 登出 URL 形如`https://YOUR_DOMAIN/samlp/CLIENT_ID/logout`。

entityID 填入 Auth0 IdP 的 Issuer，形如 `urn:xxx.auth0.com`。

在下方 SAML Response 验签证书处上传在 Auth0 下载的 IdP 证书。

点击「确定」。

在字段映射设置中选择 Auth0，点击确定。



## 在 Auth0 填入 Authing SAML SP ACS 地址

在 Applications -&gt; 应用列表条目 -&gt; Settings -&gt; Allowed Callback URLs 中填入 Authing SP 的 AssertionConsumeURL。

## 使用 Authing SAML SP 发起登录

将 Authing SAML SP 应用详情 -&gt; 使用方法选项卡 -&gt; 发起 SAML 登录项目下的地址复制到浏览器地址栏，进行访问。

浏览器会跳转到 Auth0 IdP 用户认证登录页，选择一种方式进行登录。

浏览器会跳转到在 Authing SP 设置的回调地址，以 url query 的形式附带用户信息，key 为 data。

