# 使用 SAML Service Provider

Authing 云上的 SAML Service Provider 只做两件事：替开发者发送 SAML Request 给 SAML IdP；帮开发者处理 SAML IdP 返回的 SAML Response，并将其中的用户信息以 url query 的形式发送到开发者配置的回调地址。

## 创建 SAML Service Provider

{% page-ref page="../create-saml-service-provider.md" %}

## 发起登录

![](../../../.gitbook/assets/image%20%28186%29.png)

在 SAML Service Provider 的使用方法选项卡，找到「发起 SAML 登录」，复制下面的连接，在浏览器打开访问，即可自动向配置好的 SAML IdP 发送 SAML Request。

## 用户信息回调

![](../../../.gitbook/assets/image%20%28281%29.png)

在 IdP 完成登录后，Authing SAML SP 会将解析后的用户信息以 url query 的形式发送到回调地址，数据格式为 JSON。

## 检验用户信息签名

为保证回调信息真实性，Authing 使用 SAML SP Secret 对返回的用户信息进行签名。

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

