# 创建 SAML Identity Provider 应用

## 注册一个 Authing 账号并新建一个应用

使用 Authing 云上的 SAML SP 或 SAML IdP 需要先[注册一个 Authing 账号](https://authing.cn/login)并[新建一个应用](https://authing.cn/dashboard)。

## 创建 SAML IdP 应用

进入刚创建应用的控制台，点击第三方登录选项卡，在下方的的选项卡点击 SAML IdP，点击右侧「创建 SAML IdP」按钮。

![Authing &#x63A7;&#x5236;&#x53F0;](../../.gitbook/assets/image%20%2855%29.png)

在弹出的窗口中输入应用信息，认证地址处填写一个二级域名，作为 IdP 的云上地址。点击「创建应用」。

![](../../.gitbook/assets/image%20%28272%29.png)

**认证地址**是本 IdP 应用的云上地址，这里可任意填写一个二级域名。

创建成功后，还需要填写一些 IdP 的基础配置才能使其正常工作。如果没有特殊需求，保留默认配置即可。

![IdP &#x57FA;&#x7840;&#x914D;&#x7F6E;](../../.gitbook/assets/image%20%28174%29.png)

**AssertionConsumeServiceURL** 由 SP 提供，IdP 默认会将 SAML Response POST 发到这个 URL 地址。

**支持的 bindings** 本 IdP 支持接收 SAML Request 的方式，即允许 SP 通过 GET 或 POST 的方式传递 SAML Request 信息。

SP 以 GET 方式发送 SAML Request 👇

```text
https://idp3.authing.cn/oauth/saml/idp/5ce8b87371c12a525243e4c8/SingleSignOnService?SAMLRequest=fZLNbtswEITveQqBd4n6bSTCUmDEKGAgaYu47aE3mlw7BKilSq7S9O1LqzaQIEhuxO585M4OVzfPo02ewAfjsGdFlrOb4WoV5GgnsZ7pER%2Fg9wyBkqjDIJZGz2aPwslggkA5QhCkxG59fyfKLBeTd%2BSUs%2BwF8jEhQwBPcYAz8rF6BJJakmTJ%2BsLdOgzzCH4H%2Fsko%2BPFw17NHoikIzpV1s84IUAFSptzIrTsa5KexWLKJ1gxKWtxfEKOnKpPRvMFjppC703kBTi3eKGj37XV1XaiilE3ZlHUFtWr5Luot7MwRv%2BJ5FJZsNz2bDs9d1RRayzKFHHQKsFfp%2FlPdpbrSbV3HG9t9dLQNYYYtBpJIPSvzokvzJi2773klikpU3S%2BW%2FLyEFZfBhiUqsXB%2BeON1xV%2B2z7F%2BiSvdbr45a9Tf5LPzo6T3N15kxVIxOj0sUjFjmECZgwEdI7DW%2Fbn1IAl6Rn4Gxof%2Fj77%2BPsPVPw%3D%3D
```

SP 以 POST 方式发送 SAML Request 👇

```bash
curl 'https://idp1.authing.cn/oauth/saml/idp/5ce2afd11c4f9813a24d214a/SingleSignOnService' -H 'Connection: keep-alive' -H 'Pragma: no-cache' -H 'Cache-Control: no-cache' -H 'Origin: https://sp1.authing.cn' -H 'Upgrade-Insecure-Requests: 1' -H 'Content-Type: application/x-www-form-urlencoded' -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.157 Safari/537.36' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7' --data 'SAMLRequest=PHNhbWxwOkF1dGhuUmVxdWVzdCB4bWxuczpzYW1scD0idXJuOm9hc2lzOm5hbWVzOnRjOlNBTUw6Mi4wOnByb3RvY29sIiB4bWxuczpzYW1sPSJ1cm46b2FzaXM6bmFtZXM6dGM6U0FNTDoyLjA6YXNzZXJ0aW9uIiBJRD0iX2U5NzFjOTg1LTE0YWUtNDFlZC1hMzRjLWJlNWRlOWE2ODY4MSIgVmVyc2lvbj0iMi4wIiBJc3N1ZUluc3RhbnQ9IjIwMTktMDUtMjlUMDM6MTc6NDUuMTAyWiIgRGVzdGluYXRpb249Imh0dHBzOi8vaWRwMS5hdXRoaW5nLmNuL29hdXRoL3NhbWwvaWRwLzVjZTJhZmQxMWM0Zjk4MTNhMjRkMjE0YS9TaW5nbGVTaWduT25TZXJ2aWNlIiBQcm90b2NvbEJpbmRpbmc9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDpiaW5kaW5nczpIVFRQLVBPU1QiIEFzc2VydGlvbkNvbnN1bWVyU2VydmljZVVSTD0iaHR0cHM6Ly9zcDEuYXV0aGluZy5jbi9vYXV0aC9zYW1sL3NwLzVjZTAxMjU1ZTA3OThmZTZlZGY4MWIxNS9hY3MiPjxzYW1sOklzc3Vlcj5odHRwczovL3NwMS5hdXRoaW5nLmNuL29hdXRoL3NhbWwvc3AvNWNlMDEyNTVlMDc5OGZlNmVkZjgxYjE1L21ldGFkYXRhPC9zYW1sOklzc3Vlcj48c2FtbHA6TmFtZUlEUG9saWN5IEZvcm1hdD0idXJuOm9hc2lzOm5hbWVzOnRjOlNBTUw6Mi4wOm5hbWVpZC1mb3JtYXQ6cGVyc2lzdGVudCIgQWxsb3dDcmVhdGU9ImZhbHNlIi8%2BPC9zYW1scDpBdXRoblJlcXVlc3Q%2B&RelayState=' --compressed --insecure
```

**支持的 NameID 格式** 本 IdP 支持返回的用户唯一标识符格式。SP 可能要求 IdP 返回特定格式的用户唯一标识。

如果设置 IdP 支持 emailAddress 格式，当 SP 要求返回 emailAddress 格式的用户标识符时，会返回下面这样的

```markup
<saml:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" NameQualifier="https://idp.ssocircle.com" SPNameQualifier="https://sp1.authing.cn/oauth/saml/sp/5cc5a0a22e73be044ba7f649/metadata">services@authing.cn</saml:NameID>
```

默认会返回 persistent 格式的用户标识符

```markup
<saml:NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">authing|5cc32b28d6ebae2f240a9bfd</saml:NameID>
```

**SAML Attribute** 指定本 IdP 在返回的 SAML Response 中包含哪些用户信息。

用户信息返回示例

```markup
<saml:AttributeStatement>
  <saml:Attribute Name="email" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">services@authing.cn
    </saml:AttributeValue>
  </saml:Attribute>
  <saml:Attribute Name="photo" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">https://usercontents.authing.cn/authing-avatar.png
    </saml:AttributeValue>
  </saml:Attribute>
  <saml:Attribute Name="username" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">test1
    </saml:AttributeValue>
  </saml:Attribute>
  <saml:Attribute Name="nickname" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string"/>
  </saml:Attribute>
  <saml:Attribute Name="identifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">authing|5cc32b28d6ebae2f240a9bfd
    </saml:AttributeValue>
  </saml:Attribute>
  <saml:Attribute Name="provider" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">authing
    </saml:AttributeValue>
  </saml:Attribute>
</saml:AttributeStatement>
```

**SP Metadata** 你的 SP 提供方会提供一份元数据 XML 文档，推荐上传此配置文件，Authing 会优先应用这里面的配置。

接下来配置一些 SAML Assertion 中的 Attributes 的配置。如无特殊要求，保持默认配置即可。

![](../../.gitbook/assets/image%20%28462%29.png)

**Assertion attributes 中的 Name 格式** 影响 SAML Response 中 Attribute 的 Name。

设置为 Basic 时

```markup
  <saml:Attribute Name="username" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">test1
    </saml:AttributeValue>
  </saml:Attribute>
```

设置为 URI 时

```markup
  <saml:Attribute Name="https://schemas.authing.cn/username" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
    <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">test1
    </saml:AttributeValue>
  </saml:Attribute>
```

**自定义 Attribute** 用来设置 SAML Assertion 额外携带的属性，在编辑器中输入一个合法的 JSON 对象，key 为 Attribute 的 Name，value 为 XML 中对应的值。

**认证上下文**用来描述本 IdP 签发 Assertion 时和用户通信的状态，例如 Password-ProtectedTransport 代表用户是通过 https 和 idp 连接并认证。如果无特殊需求，保留默认即可。

最后配置一些加密、签名方面的设置

#### 对 SAML Response 签名

IdP 必须对每个 SAML Response 进行签名再返回给 SP，确保断言颁发者的身份合法性。选择签名算法和摘要算法，输入或从文件读入证书内容，注意证书算法必须与选择的签名和摘要算法**一致**。如果你没有证书，可以从[这里](https://www.samltool.com/self_signed_certs.php)生成一个。

![&#x7B7E;&#x540D;&#x8BBE;&#x7F6E;](../../.gitbook/assets/image%20%28531%29.png)

#### 要求 SAML Request 签名

IdP 可以要求 SP 对 SAML Request 签名。打开此开关后，IdP 收到 SAML Request 后会先验证签名，如果签名错误，会返回错误信息，而不会执行验证用户身份的流程。

![&#x9A8C;&#x7B7E; SAML Request](../../.gitbook/assets/image%20%2874%29.png)

此处需要上传或输入相应 SP 的签名证书。SP 可能会单独提供此证书，如果没有，可以从 SP 的元数据 XML 文档中获取，注意对其进行[格式化](https://www.samltool.com/format_x509cert.php)，**保留**首尾这些`-----BEGIN CERTIFICATE-----` 字样。

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

#### 加密 SAML Response

IdP 可以对 SAML Response 进行加密。打开此开关后，IdP 会将 SAML Response 进行加密处理，再返回给 SP。

![&#x52A0;&#x5BC6; SAML Response](../../.gitbook/assets/image%20%28444%29.png)

此处需要上传或输入相应 SP 的加密证书。SP 可能会单独提供此证书，如果没有，可以从 SP 的元数据 XML 文档中获取，注意对其进行[格式化](https://www.samltool.com/format_x509cert.php)。**保留**首尾这些`-----BEGIN CERTIFICATE-----` 字样。

```markup
<KeyDescriptor use="encryption">
  <ds:KeyInfo xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    <ds:X509Data>
      <ds:X509Certificate>
        ... 输入这里的内容，先进行格式化 ...
      </ds:X509Certificate>
    </ds:X509Data>
  </ds:KeyInfo>
</KeyDescriptor>
```

点击确定，会显示本 IdP 的一些使用信息。

![&#x4F7F;&#x7528;&#x65B9;&#x6CD5;](../../.gitbook/assets/image%20%28621%29.png)

到此完成了 SAML Identity Provider 的创建。

