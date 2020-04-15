# 与 SSOCircle SAML IdP 对接

## 注册 SSOCircle 账号

[注册](https://idp.ssocircle.com/sso/UI/Login)一个 SSOCircle 账号并登录

[下载](https://idp.ssocircle.com) SSOCircle 的 IdP Metadata

## 在 Authing 配置 SP

创建一个 Authing SP。

进入应用控制台 -&gt; 第三方登录 -&gt; 社会化登录，在 SAML Service Provider 区域，点击「+」。

填写应用信息，为云上的 SP 指定一个二级域名，点击「确定」。

下面这些信息都可以在 SSOCircle IdP Metadata 中找到。

在 IdP 登录 URL 填入 SSOCircle IdP 的登录地址（这里以 HTTP-Redirect bindings 为例）

`https://idp.ssocircle.com:443/sso/SSORedirect/metaAlias/publicidp`

在 IdP 登出 URL 填入SSOCircle IdP 登出 URL（这里以 HTTP-Redirect bindings 为例）

`https://idp.ssocircle.com:443/sso/IDPSloRedirect/metaAlias/publicidp`

![](../../../.gitbook/assets/image%20%28350%29.png)

entityID 填入 SSOCircle IdP 的 entityID，`https://idp.ssocircle.com`。

IdpMetadata 填入刚刚下载过的 SSOCircle 的 IdP Metadata 文档内容

![](../../../.gitbook/assets/image%20%28153%29.png)

在下方 SAML Response 验签证书处上传 SSOCircle IdP 的签名证书。

```text
-----BEGIN CERTIFICATE-----
MIIEYzCCAkugAwIBAgIDIAZmMA0GCSqGSIb3DQEBCwUAMC4xCzAJBgNVBAYTAkRF
MRIwEAYDVQQKDAlTU09DaXJjbGUxCzAJBgNVBAMMAkNBMB4XDTE2MDgwMzE1MDMy
M1oXDTI2MDMwNDE1MDMyM1owPTELMAkGA1UEBhMCREUxEjAQBgNVBAoTCVNTT0Np
cmNsZTEaMBgGA1UEAxMRaWRwLnNzb2NpcmNsZS5jb20wggEiMA0GCSqGSIb3DQEB
AQUAA4IBDwAwggEKAoIBAQCAwWJyOYhYmWZF2TJvm1VyZccs3ZJ0TsNcoazr2pTW
cY8WTRbIV9d06zYjngvWibyiylewGXcYONB106ZNUdNgrmFd5194Wsyx6bPvnjZE
ERny9LOfuwQaqDYeKhI6c+veXApnOfsY26u9Lqb9sga9JnCkUGRaoVrAVM3yfghv
/Cg/QEg+I6SVES75tKdcLDTt/FwmAYDEBV8l52bcMDNF+JWtAuetI9/dWCBe9VTC
asAr2Fxw1ZYTAiqGI9sW4kWS2ApedbqsgH3qqMlPA7tg9iKy8Yw/deEn0qQIx8Gl
VnQFpDgzG9k+jwBoebAYfGvMcO/BDXD2pbWTN+DvbURlAgMBAAGjezB5MAkGA1Ud
EwQCMAAwLAYJYIZIAYb4QgENBB8WHU9wZW5TU0wgR2VuZXJhdGVkIENlcnRpZmlj
YXRlMB0GA1UdDgQWBBQhAmCewE7aonAvyJfjImCRZDtccTAfBgNVHSMEGDAWgBTA
1nEA+0za6ppLItkOX5yEp8cQaTANBgkqhkiG9w0BAQsFAAOCAgEAAhC5/WsF9ztJ
Hgo+x9KV9bqVS0MmsgpG26yOAqFYwOSPmUuYmJmHgmKGjKrj1fdCINtzcBHFFBC1
maGJ33lMk2bM2THx22/O93f4RFnFab7t23jRFcF0amQUOsDvltfJw7XCal8JdgPU
g6TNC4Fy9XYv0OAHc3oDp3vl1Yj8/1qBg6Rc39kehmD5v8SKYmpE7yFKxDF1ol9D
KDG/LvClSvnuVP0b4BWdBAA9aJSFtdNGgEvpEUqGkJ1osLVqCMvSYsUtHmapaX3h
iM9RbX38jsSgsl44Rar5Ioc7KXOOZFGfEKyyUqucYpjWCOXJELAVAzp7XTvA2q55
u31hO0w8Yx4uEQKlmxDuZmxpMz4EWARyjHSAuDKEW1RJvUr6+5uA9qeOKxLiKN1j
o6eWAcl6Wr9MreXR9kFpS6kHllfdVSrJES4ST0uh1Jp4EYgmiyMmFCbUpKXifpsN
WCLDenE3hllF0+q3wIdu+4P82RIM71n7qVgnDnK29wnLhHDat9rkC62CIbonpkVY
mnReX0jze+7twRanJOMCJ+lFg16BDvBcG8u0n/wIDkHHitBI7bU1k6c6DydLQ+69
h8SCo6sO9YuD+/3xAGKad4ImZ6vTwlB4zDCpu6YgQWocWRXE+VkOb+RBfvP755PU
aLfL63AFVlpOnEpIio5++UjNJRuPuAA=
-----END CERTIFICATE-----
```

![](../../../.gitbook/assets/image%20%28229%29.png)

其他配置保留默认。

点击「确定」。

在字段映射设置中选择 Auth0，点击确定。

下载 Authing SAML SP 的 Metadata。

## 在 SSOCircle 上传 Authing SP Metadata

登录 SSOCircle 后，进入左侧菜单 Manage Metadata -&gt; Add new Service Provider

FQDN 处输入 Authing SP 应用的云上地址（不含 `https://`）。

勾选一些需要返回的属性。

将 Authing SAML SP Metadata 复制到文本区域。

点击「Submit」。

![](../../../.gitbook/assets/image%20%28375%29.png)

## 使用 Authing SAML SP 发起登录

将 Authing SAML SP 应用详情 -&gt; 使用方法选项卡 -&gt; 发起 SAML 登录项目下的地址复制到浏览器地址栏，进行访问。

![](../../../.gitbook/assets/image%20%28415%29.png)

浏览器会跳转到 SSOCircle IdP 用户认证登录页，选择一种方式进行登录。此 IdP 需要科学上网使用，否则确权页面无法加载 Google 的资源。

![&#x5728; SSOCircle &#x5B8C;&#x6210;&#x767B;&#x5F55;](../../../.gitbook/assets/image%20%28105%29.png)

浏览器会跳转到在 Authing SP 设置的回调地址，以 url query 的形式附带用户信息，key 为 data。

![](../../../.gitbook/assets/image%20%28299%29.png)

