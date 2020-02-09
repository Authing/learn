---
description: Authing 提供基于 REST 的扫码登录接口，开发者可以直接调用。
---

# 完整接口列表

{% api-method method="get" host="https://oauth.authing.co/qrcode/gene?" path="userPoolId=xxx&scene=APP\_AUTH&customVar1=xxx&customVar2=xxx" %}
{% api-method-summary %}
生成二维码
{% endapi-method-summary %}

{% api-method-description %}
返回二维码 ID 和二维码链接。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="customeVar" type="string" required=false %}
自定义数据字段，可填任意多个。
{% endapi-method-parameter %}

{% api-method-parameter name="scene" type="string" required=true %}
场景值。为常量值，填 APP\_AUTH。
{% endapi-method-parameter %}

{% api-method-parameter name="userPoolId" type="string" required=true %}
用户池 ID。
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    "code": 200, // 200 表示正常
    "message": "生成二维码成功",
    "data": {
        "qrcodeId": "5e061366445c9985e5bf890a", // 二维码 ID
        "qrcodeUrl": "https://usercontents.authing.co/qrcode/5e061366445c9985e5bf890a.png" // 二维码链接
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

请求示例：[https://oauth.authing.co/qrcode/gene?userPoolId=59f86b4832eb28071bdd9214&scene=APP\_AUTH&customVar1=xxx&customVar2=xxx](https://oauth.authing.co/qrcode/gene?userPoolId=59f86b4832eb28071bdd9214&scence=APP_AUTH&customVar1=xxx&customVar2=xxx)

生成的二维码：

![](../../.gitbook/assets/image%20%28256%29.png)

使用[在线二维码解码工具](https://cli.im/deqr) 查看二维码数据如下：其中 customVar1 和 customVar2 保存到了 userDefinedData 对象中。

```text
{ 
   "scene":"APP_AUTH",
   "qrcodeId":"5e06c47da6b47a9433f8b3be",
   "userPoolId":"59f86b4832eb28071bdd9214",
   "createdAt":"2019-12-28T02:57:01.697Z",
   "expireAt":"2019-12-28T02:59:01.697Z",
   "userDefinedData":{ 
      "customVar1":"xxx",
      "customVar2":"xxx"
   }
}
```

{% api-method method="get" host="https://oauth.authing.co/qrcode/check?" path="qrcodeId=xxxx&scene=APP\_AUTH" %}
{% api-method-summary %}
查询二维码状态
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="scene" type="string" required=true %}
场景值。为常量值，填 APP\_AUTH。
{% endapi-method-parameter %}

{% api-method-parameter name="qrcodeId" type="string" required=true %}
二维码 ID。
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "code": 200, // 业务状态码，200 表示正常
  "message": "查询二维码状态成功",
  "data": {
    "qrcodeId": "5e061489445c9985e5bf890d",
    "scanned": false,
    "expired": false,
    "success": false,
    "canceled": false,
    "status": 0,
    "userInfo": {},
    "ticket": "", // 该字段不一定会有
    "description": "二维码还没有被扫描"
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

请求结果字段说明：

* scanned: 二维码是否已经被扫描
* expired：二维码是否已经过期
* success：用户是否成功授权
* canceled：用户是否取消授权
* status
  * 0: 未知状态，即还未扫码，或者已经扫码但用户还没有点击同意授权或者取消授权。
  * 1: 用户同意授权
  * -1: 用户取消授权
* userInfo:
  * 默认情况下，在用户扫码之后，会包含昵称（nickname）和头像（photo）两个字段
  * 开发者也可以配置返回完整用户信息（包括登录凭证 token）
* ticket：用于换取完整用户资料。**此字段只有在用户同意授权之后才会出现。**详情见下文。

{% api-method method="post" host="https://oauth.authing.co/oauth/scan-qrcode/exchangeUserInfoWithTicket" path="" %}
{% api-method-summary %}
使用 ticket 换取用户信息
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="ticket" type="string" required=true %}
查询二维码状态接口返回的 ticket
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{ 
   "code":200,
   "message":"换取用户信息成功",
   "data":{ 
      "_id":"5e05bbf2d51b3761d5c71070",
      "email":"983132@qq.com",
      "emailVerified":false,
      "oauth":"",
      "registerMethod":"default:username-password",
      "username":"983132@qq.com",
      "nickname":"",
      "company":"",
      "photo":"https://usercontents.authing.co/authing-avatar.png",
      "token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiOTgzMTMyQHFxLmNvbSIsImlxxxxxxxxx",
      "phone":"",
      "tokenExpiredAt":"2020-01-11T08:08:18.000Z",
      "loginsCount":1,
      "lastIP":"::1",
      "signedUp":"2019-12-27T08:08:18.115Z",
      "blocked":false,
      "isDeleted":false
   }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
注意：默认情况下，此接口**只允许在服务器端调用**，即需要使用用户池密钥初始化之后。

ticket 默认有效时间为 300 s。

开发者可在 [Authing 控制台](https://authing.cn/dashboard) **基础配置** -&gt; **基础设置** -&gt; **App 扫码登录 Web 自定义配置** 处修改。**详情见自定义配置项页。**
{% endhint %}

{% page-ref page="customize.md" %}

{% api-method method="post" host="https://oauth.authing.co/oauth/scan-qrcode/scanned" path="" %}
{% api-method-summary %}
APP 端标记已扫码
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
用户登录凭证。
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="qrcodeId" type="string" required=true %}
二维码 ID。
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    code: 200,
    message: "二维码扫描确认成功",
    data: {
        qrcodeId: "", // 原样返回
        status: 0, 
        description: "xxxx",
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
APP 端需要满足两个条件：

1. 用户必须处于登录态
2. 用户的用户池 ID 和二维码用户池 ID 匹配。
{% endhint %}

{% api-method method="post" host="https://oauth.authing.co/oauth/scan-qrcode/confirm" path="" %}
{% api-method-summary %}
APP 端同意授权
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
用户登录凭证。
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="qrcodeId" type="string" required=true %}
二维码 ID
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    code: 200,
    message: "授权登录成功",
    data: {
        qrcodeId: "", // 原样返回
        status: 1, 
        description: "xxxx",
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
APP 端需要满足两个条件：

1. 用户必须处于登录态
2. 用户的用户池 ID 和二维码用户池 ID 匹配。
{% endhint %}

{% api-method method="post" host="https://oauth.authing.co/oauth/scan-qrcode/cancel" path="" %}
{% api-method-summary %}
APP 端取消授权
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
用户登录凭证。
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="qrcodeId" type="string" required=true %}
二维码 ID
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    code: 200,
    message: "取消授权成功",
    data: {
        qrcodeId: "", // 原样返回
        status: -1, 
        description: "xxxx",
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
APP 端需要满足两个条件：

1. 用户必须处于登录态
2. 用户的用户池 ID 和二维码用户池 ID 匹配。
{% endhint %}

