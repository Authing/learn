# 同时使用 Authing 作为 SP 和 IdP

本教程讲述如何使用 Authing 云上的 SAML SP 和 SAML IdP 为企业或个人快速接入单点认证能力。完成本教程后，你会拥有运行在 Authing 云上的 SAML Service Provider 和 SAML Identity Provider 来处理 SAML 协议相关的过程，而你只需关注 Authing 返回的用户信息与 JWT Token。

在用户访问受保护的资源时，结合 Authing SDK 可以对他携带的 JWT Token 进行验证。如果 JWT Token 验证失败，只需将用户重定向到 SAML Service Provider 的云上地址，它会负责通过 SAML 协议与 IdP 交换信息从而认证用户，并返回 JSON 格式的用户信息。

## 创建一个 SAML IdP 应用

进入应用控制台 -&gt; 第三方登录 -&gt; SAML IdP 选项卡，点击「创建 SAML IdP」

填写应用信息，为云上的 IdP 指定一个二级域名，点击「确定」

此时我们还没有 SP，所以 AssertionConsumeServiceURL 可以先不填写真正的 appId。

![](../../.gitbook/assets/image%20%28399%29.png)

其他配置使用默认即可。

生成一对秘钥，上传。用来对 SAML Response 签名、验签。

![](../../.gitbook/assets/image%20%28401%29.png)

![](../../.gitbook/assets/image%20%28231%29.png)

创建完成后，转到使用方法页。记录 IdP 登录 URL、IdP 登出 URL 和 IdP EntityID，后续创建 SP 时会使用。

## 创建一个 SAML SP 应用

进入应用控制台 -&gt; 第三方登录 -&gt; 社会化登录，在 SAML Service Provider 区域，点击「+」。

![Authing &#x63A7;&#x5236;&#x53F0;](../../.gitbook/assets/image%20%28420%29.png)

![&#x521B;&#x5EFA; SAML SP](../../.gitbook/assets/image%20%28312%29.png)

填写应用信息，为云上的 SP 指定一个二级域名，点击「确定」 。

在 IdP 登录 URL、IdP 登出 URL、IdP EntityID 输入刚才记录的三项内容。

其他配置使用默认即可。点击「确定」。

在 IdP 字段映射方案列表中选择 Authing，点击「确定」。

创建完成后，转到使用方法页。记录 AssertionConsumeServiceURL、下载 SP Metadata。

![](../../.gitbook/assets/image%20%2811%29.png)

## 编辑刚创建的 IdP 应用

进入应用控制台 -&gt; 第三方登录 -&gt; SAML IdP 选项卡，找到刚刚创建的 IdP，点击右侧的「...」详情按钮，进入编辑。

![&#x7F16;&#x8F91; SAML IdP](../../.gitbook/assets/image%20%28241%29.png)

在基础配置选项卡，填入刚才记录的 AssertionConsumeServiceURL、上传刚才下载的 SP Metadata，点击「确定」。

![&#x586B;&#x5199;&#x57FA;&#x672C;&#x4FE1;&#x606F;](../../.gitbook/assets/image%20%28123%29.png)

在使用方法选项卡，下载 IdP Metadata。

![](../../.gitbook/assets/image%20%2875%29.png)

## 编辑刚创建的 SP 应用

进入应用控制台 -&gt; 第三方登录 -&gt; 社会化登录，在 SAML Service Provider 区域，找到刚才创建的 SP 应用，点击「编辑」。

![Authing &#x63A7;&#x5236;&#x53F0;](../../.gitbook/assets/image%20%28420%29.png)

![&#x7F16;&#x8F91; SAML SP](../../.gitbook/assets/image%20%28310%29.png)

在基础配置选项卡，上传刚才下载的 IdP Metadata。点击「确定」。

![&#x4E0A;&#x4F20; IdP Metadata](../../.gitbook/assets/image%20%28284%29.png)

## 开始使用

当你的受保护资源被访问时，需要先对用户身份进行认证，可以通过 Authing SDK 检验 JWT Token 的合法性。如果 token 不合法，可以将用户重定向到云上的 SAML SP，完成后续的用户身份认证流程，Authing 云上的 SAML SP 最终会携带着用户信息和 JWT Token 跳回在 SP 中设置的回调地址。

云上的 SAML SP 地址形式如下

让 SP 通过 GET 请求发送 SAML Request

```text
GET https://<domain>.authing.cn/oauth/saml/sp/<appId>/spinitsso-redirect
```

让 SP 通过 POST 请求发送 SAML Request

```text
GET https://<domain>.authing.cn/oauth/saml/sp/<appId>/spinitsso-post
```

{% hint style="info" %}
以下是一次完整的 SAML 登录流程
{% endhint %}

### 访问云上的 SP

在浏览器直接访问云上 SP 地址或者通过后端重定向到云上 SP 地址。

![&#x76F4;&#x63A5;&#x5728;&#x6D4F;&#x89C8;&#x5668;&#x8BBF;&#x95EE;&#x4E91;&#x4E0A; SP](../../.gitbook/assets/image%20%28298%29.png)

### 跳转到 IdP 认证页

如果用户没有登录过，会被重定向到 IdP 用户登录认证页。

![IdP &#x5728;&#x8BA4;&#x8BC1;&#x7528;&#x6237;&#x8EAB;&#x4EFD;](../../.gitbook/assets/image%20%28194%29.png)

### 用户进行登录

用户可以选择一种方式进行登录。

![&#x7528;&#x6237;&#x8FDB;&#x884C;&#x767B;&#x5F55;](../../.gitbook/assets/image%20%28440%29.png)

![&#x7528;&#x6237;&#x786E;&#x6743;](../../.gitbook/assets/image%20%28190%29.png)

### Authing 将用户信息发到回调地址

云上的 IdP 会将 SAML Response 发送到云上的 SP，SP 会处理好 SAML Response XML 信息，以 JSON 的数据格式将用户信息发到在 SP 配置的回调地址。

![](../../.gitbook/assets/image%20%2827%29.png)

### 检验用户信息签名

以下是一个发送到回调地址数据的示例

```text
https://authing.cn/?data=%7B%22_id%22%3A%225e1c31b00c9ba94f7e2b4046%22%2C%22email%22%3A%22asdf%40123.com%22%2C%22emailVerified%22%3Afalse%2C%22unionid%22%3A%22saml%7Cauthing%7Cauthing%7C5e1c31300c9ba94f7e2b4007%22%2C%22openid%22%3Anull%2C%22registerMethod%22%3A%22saml%3Aidp%3A%3Aauthing%22%2C%22username%22%3A%22asdfasdf%22%2C%22nickname%22%3A%22%22%2C%22company%22%3A%22%22%2C%22photo%22%3A%22https%3A%2F%2Fusercontents.authing.cn%2Fauthing-avatar.png%22%2C%22token%22%3A%22eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiYXNkZkAxMjMuY29tIiwidW5pb25pZCI6InNhbWx8YXV0aGluZ3xhdXRoaW5nfDVlMWMzMTMwMGM5YmE5NGY3ZTJiNDAwNyIsImlkIjoiNWUxYzMxYjAwYzliYTk0ZjdlMmI0MDQ2IiwiY2xpZW50SWQiOiI1ZTFjMmEzMzA4YTljNTQ1Y2IxZGMyZWEifSwiaWF0IjoxNTc4OTE3MjEyLCJleHAiOjE1ODAyMTMyMTJ9.3wAuO3Ql53ywaG9Wjm_sxfMlTaU8BnvQgwyJqPTz6n4%22%2C%22tokenExpiredAt%22%3A%222020-01-28T20%3A06%3A52%2B08%3A00%22%2C%22loginsCount%22%3A3%2C%22lastLogin%22%3A%222020-01-13T20%3A06%3A52%2B08%3A00%22%2C%22lastIP%22%3A%22127.0.0.1%22%2C%22signedUp%22%3A%222020-01-13T17%3A00%3A32%2B08%3A00%22%2C%22blocked%22%3Afalse%2C%22isDeleted%22%3Afalse%7D&signature=b84f601b8254251437f2f2ee650b64a1d16a14e6a1b6c397f3eef55abfa2cd6a&code=200&returnTo=
```

在 url 的 query 中有一个 signature 字段

```text
signature=b84f601b8254251437f2f2ee650b64a1d16a14e6a1b6c397f3eef55abfa2cd6a
```

开发者需要检验这个字段是否合法来判断用户信息真实来自 Authing，而不是来自伪造的请求。

签名方式为 HMAC-SHA256，生成签名的伪代码如下：

```text
signature = HMAC-SHA256(<SAML SP 应用 secret>, <用户信息 JSON 字符串>)
```

注意需要提**取出** url 中 **data** 字段，并进行 **unescape** 操作，将结果作为参数放在 &lt;用户信息 JSON 字符串&gt;。&lt;用户信息 JSON 字符串&gt; 中**不能有 % 等 url 中的转义字符，必须是一个 JSON 格式字符串**。

Node.JS 代码示例如下：

```javascript
let resultUrl = 'https://authing.cn/?data=%7B%22_id%22%3A%225e1c31b00c9ba94f7e2b4046%22%2C%22email%22%3A%22asdf%40123.com%22%2C%22emailVerified%22%3Afalse%2C%22unionid%22%3A%22saml%7Cauthing%7Cauthing%7C5e1c31300c9ba94f7e2b4007%22%2C%22openid%22%3Anull%2C%22registerMethod%22%3A%22saml%3Aidp%3A%3Aauthing%22%2C%22username%22%3A%22asdfasdf%22%2C%22nickname%22%3A%22%22%2C%22company%22%3A%22%22%2C%22photo%22%3A%22https%3A%2F%2Fusercontents.authing.cn%2Fauthing-avatar.png%22%2C%22token%22%3A%22eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiYXNkZkAxMjMuY29tIiwidW5pb25pZCI6InNhbWx8YXV0aGluZ3xhdXRoaW5nfDVlMWMzMTMwMGM5YmE5NGY3ZTJiNDAwNyIsImlkIjoiNWUxYzMxYjAwYzliYTk0ZjdlMmI0MDQ2IiwiY2xpZW50SWQiOiI1ZTFjMmEzMzA4YTljNTQ1Y2IxZGMyZWEifSwiaWF0IjoxNTc4OTE3MjEyLCJleHAiOjE1ODAyMTMyMTJ9.3wAuO3Ql53ywaG9Wjm_sxfMlTaU8BnvQgwyJqPTz6n4%22%2C%22tokenExpiredAt%22%3A%222020-01-28T20%3A06%3A52%2B08%3A00%22%2C%22loginsCount%22%3A3%2C%22lastLogin%22%3A%222020-01-13T20%3A06%3A52%2B08%3A00%22%2C%22lastIP%22%3A%22127.0.0.1%22%2C%22signedUp%22%3A%222020-01-13T17%3A00%3A32%2B08%3A00%22%2C%22blocked%22%3Afalse%2C%22isDeleted%22%3Afalse%7D&signature=b84f601b8254251437f2f2ee650b64a1d16a14e6a1b6c397f3eef55abfa2cd6a&code=200&returnTo='
let str = querystring.parse(url.parse(resultUrl).query).data


const hmac = crypto.createHmac('sha256', '75ff71126beb73236396aa74baa4195c');
hmac.update(str)
console.log(hmac.digest('hex') === querystring.parse(url.parse(resultUrl).query).signature)
```

