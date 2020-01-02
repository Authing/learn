# 创建 SAML Service Provider 应用

## 注册一个 Authing 账号并新建一个应用

使用 Authing 云上的 SAML SP 或 SAML IdP 需要先[注册一个 Authing 账号](https://authing.cn/login)并[新建一个应用](https://authing.cn/dashboard)。

## 创建 SAML SP 应用

打开应用控制台 -&gt; 第三方登录 -&gt; 社会化登录选项卡

![&#x5E94;&#x7528;&#x63A7;&#x5236;&#x53F0;](../../.gitbook/assets/image%20%28127%29.png)

滚动到页面下方，在 SAML Service Provider 区域，点击 ➕

![&#x521B;&#x5EFA; SAML SP](../../.gitbook/assets/image%20%28188%29.png)

在弹出的窗口中，输入应用信息，点击「创建应用」。

![&#x5E94;&#x7528;&#x4FE1;&#x606F;](../../.gitbook/assets/image%20%2839%29.png)

**认证地址**是本 SP 的云上地址，可以任意填写一个二级域名。

**回调地址**是完成 SAML 认证后，用户信息被发送到的地址，会以 query 的形式传递，key 为 data。

接下来需要填写一些 SP 的基础配置才能使 SP 正常工作。

![&#x57FA;&#x7840;&#x914D;&#x7F6E;](../../.gitbook/assets/image%20%28330%29.png)

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

如果提供了多个 URL，建议填写 HTTP-Redirect 对应的 URL。登出时只需访问 `https://<domain>.authing.cn/profile/logout`完成 token 清除

**IdP EntityID** 由 IdP 提供，如果没有，可以从 IdP 元数据 XML 文档中查找。

```markup
<EntityDescriptor entityID="https://idp.ssocircle.com" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

**IdP Metadata** 是由 IdP 提供元数据 XML 文档，建议上传此配置文件，Authing 会优先应用此文件的配置。

接下来，需要配置一些 SP 对 IdP 的“要求”。

![SP &#x5BF9; IdP &#x7684;&#x201C;&#x8981;&#x6C42;&#x201D;](../../.gitbook/assets/image%20%28176%29.png)

 **要求的用户唯一标识符形式** SP 可以要求 IdP 返回特定格式的用户唯一标识符。默认为 unspecified，会返回 persistent 类型的用户 Id。

例如，选择 persistent 时，IdP 会返回下面格式的用户 Id 类型

```markup
<saml:NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">authing|5cc32b28d6ebae2f240a9bfd</saml:NameID>
```

而设置为 emailAddress 时，返回的用户标识符就变成了下面这样

```markup
<saml:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" NameQualifier="https://idp.ssocircle.com" SPNameQualifier="https://sp1.authing.cn/oauth/saml/sp/5cc5a0a22e73be044ba7f649/metadata">services@authing.cn</saml:NameID>
```

**协议绑定** 要求 IdP 通过 POST 请求，还是 GET 请求把 SAML Response 发到云上 SP 的 Assertion Consume Service 接口。一般使用 POST 并设为默认。

为什么可以在 SP 中配置 IdP 呢？因为 IdP 方可能会要求你上传 SP 的元数据 XML 文档，从而做出不同的行为。这里的配置会用来生成本 SP 的元数据文档。反之亦然。

不必担心，你无须关心这些 XML 的处理。云上的 SP 都会自动处理，最终返回到回调网站的是一串 JSON 格式的用户信息。

最后，有三个开关，分别用于验签、解密、签名。

#### 验签 SAML Response

作为 SP，必须对 IdP 颁发的 SAML Assertion 验签从而提升安全性。输入或者从文件读入 IdP 签名证书，这样 SP 收到 SAML Assertion 后就会检验签名合法性，确保是真实的 IdP 颁发的断言，如果签名不正确，SP 会跳转到回调网址，错误信息以 query 的形式传递，不会返回用户信息。

![&#x9A8C;&#x7B7E; SAML Response](../../.gitbook/assets/image%20%2896%29.png)

此处需要上传或输入相应 IdP 的签名证书。IdP 可能会单独提供此证书，如果没有，可以从 IdP 的元数据 XML 文档中获取，注意对其进行[格式化](https://www.samltool.com/format_x509cert.php)。

```markup
<KeyDescriptor use="signing">
  <ds:KeyInfo xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    <ds:X509Data>
      <ds:X509Certificate>
        ... 输入这里的内容，先进行格式化 ...
      </ds:X509Certificate>
    </ds:X509Data>
  </ds:KeyInfo>
</KeyDescriptor>
```

#### 解密 SAML Response

SP 可以要求 IdP 对 SAML Assertion 进行加密，SP 需要为 IdP 提供加密公钥，IdP 进行加密。SP 使用自己的私钥解密内容。打开开关，输入或从文件读入加密证书秘钥对，随后需要将公钥，或者 SP 元数据 XML 文档交给 IdP，在你的 IdP 提供商进行相应配置。

![&#x52A0;&#x5BC6; SAML Response](../../.gitbook/assets/image%20%28120%29.png)

输入或从文件读入证书内容，注意证书算法必须与选择的签名和摘要算法**一致**。如果你没有证书，可以从[这里](https://www.samltool.com/self_signed_certs.php)生成一个。

#### 签名 SAML Request

SP 可以对发出的 SAML Request 进行签名，确保此 SAML Request 的发出者身份合法。IdP 可能通过 SAML Request 签名来判断请求来源，从而拒绝非法的 SALM Request 请求。

![&#x7B7E;&#x540D; SAML Request](../../.gitbook/assets/image%20%28246%29.png)

输入或从文件读入证书内容，注意证书算法必须与选择的签名和摘要算法**一致**。如果你没有证书，可以从[这里](https://www.samltool.com/self_signed_certs.php)生成一个。

基础信息配置完成，点击确定。

### 字段映射

当 IdP 和用户完成了身份验证，IdP 会把用户信息放在 SAML Response 的 Assertion 中发给 SP。Authing 需要将其中的数据存入自己的数据库，这就需要字段对齐。Authing 需要知道 SAML Response 中的用户信息字段名称是什么，进行取出。Authing 内置了一些 IdP 的字段对齐配置，你需要从中选择你的 IdP 提供商。

![&#x5185;&#x7F6E; IdP &#x6620;&#x5C04;&#x65B9;&#x6848;](../../.gitbook/assets/image%20%28104%29.png)

如果找不到你的 IdP 提供商或内置的字段对齐方案不能满足需求，你可以使用自定义字段对齐配置

![&#x914D;&#x7F6E;&#x5B57;&#x6BB5;&#x6620;&#x5C04;&#xFF0C;&#x81EA;&#x5B9A;&#x4E49;&#x65B9;&#x6848;](../../.gitbook/assets/image%20%284%29.png)

左侧是 Authing 数据库对应的字段，右侧是 Assertion 中的字段，你需要把 Assertion 中对应的字段名称填入对应的位置，以便 Authing 可以为你返回正确的 JSON 格式用户信息。

例如，对于以下 SSOCircle 返回的用户信息的 Assertion

```markup
<saml:AttributeStatement>
	<saml:Attribute Name="EmailAddress">
		<saml:AttributeValue
			xmlns:xs="http://www.w3.org/2001/XMLSchema"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">example@authing.cn
		</saml:AttributeValue>
	</saml:Attribute>
	<saml:Attribute Name="UserID">
		<saml:AttributeValue
			xmlns:xs="http://www.w3.org/2001/XMLSchema"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">example
		</saml:AttributeValue>
	</saml:Attribute>
	<saml:Attribute Name="FirstName">
		<saml:AttributeValue
			xmlns:xs="http://www.w3.org/2001/XMLSchema"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">vincent
		</saml:AttributeValue>
	</saml:Attribute>
	<saml:Attribute Name="LastName">
		<saml:AttributeValue
			xmlns:xs="http://www.w3.org/2001/XMLSchema"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">smith
		</saml:AttributeValue>
	</saml:Attribute>
</saml:AttributeStatement>
```

需要填的字段对齐内容为

IdP 名字：ssocircle，可以填任意名字

email：EmailAddress

username：FirstName

nickanme：FirstName

photo：不填，因为 Assertion 中无此信息

company：不填，因为 Assertion 中无此信息

如果你既没在列表中找到自己的 IdP 提供商，也不知道 IdP 返回的 SAML Response 是什么格式的，你需要去 IdP 提供商查看一下他们提供的 SAML Response 示例，来确定上面需要填写的字段。

完成设置后，点击确定，会展示一些使用提示。

![SAML SP &#x4F7F;&#x7528;&#x65B9;&#x6CD5;](../../.gitbook/assets/image%20%28162%29.png)

到此 SAML Service Provider 创建完成。

