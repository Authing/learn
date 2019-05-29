# 创建 SAML Service Provider 应用

## 注册一个 Authing 账号并新建一个应用

使用 Authing 云上的 SAML SP 或 SAML IdP 需要先[注册一个 Authing 账号](https://authing.cn/login)并[新建一个应用](https://authing.cn/dashboard)。

## 创建 SAML SP 应用

打开应用控制台-&gt;第三方登录-&gt;社会化登录选项卡

![&#x5E94;&#x7528;&#x63A7;&#x5236;&#x53F0;](../../.gitbook/assets/image%20%2883%29.png)

滚动到页面下方，在 SAML Service Provider 区域，点击 ➕

![&#x521B;&#x5EFA; SAML SP](../../.gitbook/assets/image%20%2817%29.png)

在弹出的窗口中，输入应用信息，点击「创建应用」。

![&#x5E94;&#x7528;&#x4FE1;&#x606F;](../../.gitbook/assets/image%20%2867%29.png)

**认证地址**是本 SP 的云上地址，可以任意填写一个二级域名。

**回调地址**是完成 SAML 认证后，用户信息被发送到的地址，会以 query 的形式传递，key 为 data。

接下来需要填写一些 SP 的基础配置才能使 SP 正常工作。

![&#x57FA;&#x7840;&#x914D;&#x7F6E;](../../.gitbook/assets/image%20%28126%29.png)

**IdP 登录 URL** 由 IdP 提供，如果没有，可以从 IdP 元数据 XML 文档中查找。

```markup
<SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://idp.ssocircle.com:443/sso/SSORedirect/metaAlias/publicidp"/>
<SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://idp.ssocircle.com:443/sso/SSOPOST/metaAlias/publicidp"/>
<SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:SOAP" Location="https://idp.ssocircle.com:443/sso/SSOSoap/metaAlias/publicidp"/>
```

如果提供了多个 URL，建议填写 HTTP-Redirect 对应的 URL。并在后续使用 `https://<domain>.authing.cn/oauth/saml/sp/<appId>/spinitsso-redirect` 地址发起 SAML 登录。

**IdP 登出 URL** 由 IdP 提供，如果没有，可以从 IdP 元数据 XML 文档中查找。

```markup
<SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://idp.ssocircle.com:443/sso/IDPSloRedirect/metaAlias/publicidp" ResponseLocation="https://idp.ssocircle.com:443/sso/IDPSloRedirect/metaAlias/publicidp"/>
<SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://idp.ssocircle.com:443/sso/IDPSloPost/metaAlias/publicidp" ResponseLocation="https://idp.ssocircle.com:443/sso/IDPSloPost/metaAlias/publicidp"/>
<SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:SOAP" Location="https://idp.ssocircle.com:443/sso/IDPSloSoap/metaAlias/publicidp"/>
```

如果提供了多个 URL，建议填写 HTTP-Redirect 对应的 URL。登出时只需访问 登出时只需访问 `https://<domain>.authing.cn/profile/logout`

**IdP EntityID** 由 IdP 提供，如果没有，可以从 IdP 元数据 XML 文档中查找。

```markup
<EntityDescriptor entityID="https://idp.ssocircle.com" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

**IdP Metadata** 是由 IdP 提供元数据 XML 文档，建议上传此配置文件，Authing 会优先应用此文件的配置。







