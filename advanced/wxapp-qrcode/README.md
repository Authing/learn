---
description: 扫描小程序二维码并使用小程序「身份管家」进行认证
---

# 接入小程序扫码登录

小程序扫码登录指使用 Authing 官方开发的小程序「小登录」执行扫码登录，使用 JavaScript SDK 可以快速接入，除此之外我们还提供了 [HTTP 接口](https://docs.authing.cn/authing/advanced/wxapp-qrcode#tiao-yong-http-api-jie-ru-xiao-cheng-xu-sao-ma-ren-zheng)。

点击此处[体验小程序扫码登录](https://sample.authing.cn/)。

![&#x626B;&#x7801;&#x793A;&#x4F8B;](../../.gitbook/assets/wxapp-scaning-demo.gif)

## 扫码登录示例代码

请参考：[https://github.com/Authing/qrcode-sample](https://github.com/Authing/qrcode-sample)。

## 接入流程

### 配置小程序信息

按照下图打开小程序配置界面：

![](../../.gitbook/assets/image%20%28409%29.png)

打开开关后将看到如下的弹出框：

![](../../.gitbook/assets/image%20%2814%29.png)

小程序扫码登录有两种配置方式：

1. 上传小程序 Logo，上传完毕后 Authing 将生成一张带有该 Logo 的二维码，开发者完成接入后，用户扫描二维码即可登录；
2. 注册一个小程序，然后将小程序的 appId 和 appSecret 配置到 Authing 平台中，开发者完成接入后，用户扫描二维码即可登录；

第二种方式如下图所示：

![](../../.gitbook/assets/image%20%28449%29.png)

相关参数解释如下：

| Client ID | Client Secret | Redirect |
| :--- | :--- | :--- |
| 小程序的 App ID | 小程序的 App Secret | 成功或失败后的回调 URL（你的业务地址） |

在弹出框中依次填入小程序的 AppID、App Secret 和回调地址并点击「确定」即可完成配置。

#### 我该选择哪种配置方式？

1. 如果你没有自己的小程序，可以直接上传 Logo 完成配置；
2. 如果你有自己的小程序，建议点击本页中的「配置自己的小程序信息」接入；
3. 若同时配置了小程序 Logo 和自己的小程序信息，会优先使用开发者「自己的小程序信息」。**若想取消使用「自己的小程序信息」，请清空相关配置。**

### 使用 SDK for Web 初始化扫码界面

####  安装 authing-js-sdk

```bash
$ npm install authing-js-sdk --save
```

更多 SDK 的使用方法请参考：

{% page-ref page="../../sdk/sdk-for-web/" %}

安装完成后，请新建一个 Web 项目，然后复制以下代码：

```javascript
const Authing = require('authing-js-sdk');

// 初始化 Authing SDK for Web
const auth = new Authing({
    clientId: 'your_client_id',
    timestamp: Math.round(new Date() / 1000),
    nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
});

auth.then(function(authing) {
    // 调用小程序扫码登录的方法，此方法将生成一个用于扫码登录的图片和相关提示信息
    // 用户扫描成功后会回调至开发者在控制台中配置的 Redirect URI
    // 若不想跳转，请将 redirect 参数设置为 false，并在 onSuccess 中处理用户信息
    authing.startWXAppScaning({
      enableFetchPhone: true, // 启用获取手机号
    
      // 可选，登录失败后的回调函数，一般为网络问题
      onError: function(error) {}, 
    });
})
```

运行成功后将生成如下图片：

![](../../.gitbook/assets/image%20%28333%29.png)

{% hint style="info" %}
在线体验方法：

1. 打开 [https://sample.authing.cn/\#/](https://sample.authing.cn/#/) 
2. 选择「扫码登录」
3. 打开微信扫描二维码即可体验
{% endhint %}

当扫码成功后将跳转到回调 URL 中，链接例如：

[https://sample.authing.cn/\#/redirect?code=200&data=balabala...very...long...](https://sample.authing.cn/#/redirect?code=200&data=%7B%22_id%22%3A%225b4d5f37f5bca40001c1206f%22,%22email%22%3Anull,%22emailVerified%22%3Afalse,%22unionid%22%3A%22oVzVG46cjx0bOf_kmtVpOqv5t0EQ%22,%22oauth%22%3A%22%7B%5C%22nickName%5C%22%3A%5C%22ivy%5C%22,%5C%22gender%5C%22%3A1,%5C%22language%5C%22%3A%5C%22en%5C%22,%5C%22city%5C%22%3A%5C%22%5C%22,%5C%22province%5C%22%3A%5C%22Beijing%5C%22,%5C%22country%5C%22%3A%5C%22China%5C%22,%5C%22avatarUrl%5C%22%3A%5C%22https%3A%2F%2Fwx.qlogo.cn%2Fmmopen%2Fvi_32%2FQ0j4TwGTfTLkQc7PfrbBqFMib6lkPUxaA5UsMiadibfWQtKv0CBcKnH2khXicvUB9WB2ibYxN6GRTaTsQfPtlsAafBg%2F132%5C%22,%5C%22openid%5C%22%3A%5C%22oVzVG46cjx0bOf_kmtVpOqv5t0EQ%5C%22%7D%22,%22registerMethod%22%3A%22oauth%3Awechat%22,%22username%22%3A%22ivy%22,%22nickname%22%3A%22ivy%22,%22company%22%3A%22%22,%22photo%22%3A%22https%3A%2F%2Fwx.qlogo.cn%2Fmmopen%2Fvi_32%2FQ0j4TwGTfTLkQc7PfrbBqFMib6lkPUxaA5UsMiadibfWQtKv0CBcKnH2khXicvUB9WB2ibYxN6GRTaTsQfPtlsAafBg%2F132%22,%22browser%22%3A%22%22,%22token%22%3A%22eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjpudWxsLCJ1bmlvbmlkIjoib1Z6Vkc0NmNqeDBiT2Zfa210VnBPcXY1dDBFUSIsImlkIjoiNWI0ZDVmMzdmNWJjYTQwMDAxYzEyMDZmIiwiY2xpZW50SWQiOiI1YTlmYTI2Y2Y4NjM1YTAwMDE4NTUyOGMifSwiaWF0IjoxNTU3OTY3Njg3LCJleHAiOjE1NTkyNjM2ODd9.dlm1ZJs9bBIkM3aa8oDcI_JfQWnkmtNzNj9rxviUDz4%22,%22tokenExpiredAt%22%3A%22Fri%20May%2031%202019%2008%3A48%3A07%20GMT%200800%20%28CST%29%22,%22loginsCount%22%3A117,%22lastLogin%22%3A%22Thu%20May%2016%202019%2008%3A48%3A07%20GMT%200800%20%28CST%29%22,%22lastIP%22%3A%22106.121.160.135%22,%22signedUp%22%3A%22Tue%20Jul%2017%202018%2011%3A15%3A03%20GMT%200800%20%28CST%29%22,%22blocked%22%3Afalse,%22isDeleted%22%3Afalse%7D)

开发者可自行获取 URL 中的参数以获取 userInfo，获取方式见[获取用户数据](https://learn.authing.cn/authing/advanced/social-login#huo-qu-yong-hu-shu-ju)。

{% hint style="info" %}
若不想在扫码登录后发生页面跳转，可以配置 redirect 参数为 false，然后在 onSuccess 函数中获取用户数据后执行相应业务，如：

```javascript
authing.startWXAppScaning({
  // 不自动跳转
  redirect: false,
  
  enableFetchPhone: true // 启用获取手机号
  
  // 扫码成功
  onSuccess(res) {
    const userInfo = res.data;
    
    // 存储 token 到 localStorage 中
    localStorage.setItem('token', userInfo.token);
  }
});
```

若你想在后端验证 Token 合法性，请参考：[验证 Token 合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。
{% endhint %}

示例代码请参考：[https://github.com/Authing/qrcode-sample](https://github.com/Authing/qrcode-sample)。

#### 完整参数说明

```javascript
authing.startWXAppScaning({
      // 可选，二维码挂载点，如不写则默认漂浮在文档中间
      mount: 'qrcode-node',
      
      // 可选，是否执行跳转（在用户后台配置的 Redirect URL），默认为 true，相关用户信息回传至 url 参数上
      redirect: true,
      
      // 可选，登录成功后回调函数，redirect 为 true 时不回调此函数
      onSuccess: function(res) {},
      
      // 可选，登录失败后回调函数，一般为网络问题
      onError: function(error) {}, 
      
      // 可选，轮询时的回调函数，intervalNum 为 setInterval 返回的数值，可使用 clearInterval 停止轮询
      onIntervalStarting: function(intervalNum) {},
      
      // 可选，当二维码展现时的回调函数
      onQRCodeShow: function(qrcode) {},
      
      // 可选，当二维码加载完成时的回调函数
      onQRCodeLoad: function(qrcode) {},
      
      // 可选，每隔多少秒检查一次，默认 1500
      interval: 1500,
      
      // 可选，提示信息，可写 HTML
      tips: '搜索小程序 <strong>身份管家</strong> 扫码登录',
      
      // 扫码成功的提示信息，默认：扫码成功
      successTips: '扫码成功',
      
      // 扫码成功后跳转前的提示信息，默认：扫码成功，即将跳转
      successRedirectTips: '扫码成功，即将跳转',
      
      // 重试扫码的提示信息，默认：重试
      retryTips: '重试', 
      
      // 扫码失败的提示信息，默认：网络出错，请重试
      failedTips: '网络出错，请重试',  
      
      // 是否支持获取手机号（使用小登录扫码）
      enableFetchPhone: false,
      
      // 私有化部署了小程序的用户请将此参数设置为 true
      useSelfWxapp: false,
});
```

若想动态修改提示信息，请使用以下四个方法：

```javascript
  // 修改重试扫码的提示信息
  authing.updateRetryTips(tips: string)

  // 修改扫码失败的提示信息
  authing.updateFailedTips(tips: string)

  // 修改扫码成功的提示信息
  authing.updateSuccessTips(tips: string)

  // 修改扫码成功后跳转前的提示信息
  authing.updateSuccessRedirectTips(tips: string)
```

{% hint style="info" %}
验证 Token 合法性请参考：[验证 Token 合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。
{% endhint %}

## 使用 Guard 初始化小程序扫码登录

Guard 是 Authing 提供的快速生成登录表单的 SDK，**若开发者在后台开启了小程序扫码登录，那么 Guard 就会自动生成小程序扫码登录的界面**，你可以[点击此处体验 Guard](https://sample.authing.cn/)。

Guard 的使用方法请参考：

{% page-ref page="../../sdk/guard.md" %}

## 调用 HTTP API 接入小程序扫码认证

HTTP 接口适用于非 JavaScript 平台，JavaScript 开发者可以略过此节，直接使用 SDK。

扫码登录需要客户端做两个步骤：

1. 生成二维码
2. 客户端轮询查询扫码状态

还有一个步骤是用户搜索`身份管家`小程序进行扫码登录，这块已由 Authing 处理。

{% api-method method="get" host="https://oauth.authing.cn" path="/oauth/wxapp/qrcode/<Authing 应用 ID>?random=RANDOM\_STRING" %}
{% api-method-summary %}
 生成二维码
{% endapi-method-summary %}

{% api-method-description %}
 此方法将根据开发者在 Authing 控制台配置的小程序信息生成带有开发者在微信后台配置的 Logo 的小程序二维码。  
  
示例 URL：  
  
https://oauth.authing.cn/oauth/wxapp/qrcode/5c344f102e450b000170190a?random=UaJe6j5S47pRTLo7
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="random" type="string" required=true %}
 客户端随机生成的字符串
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    "data": {
        "_id": "*********************",
        "client": "*********************",
        "oauth": "*********************",
        "oauthWithApplication": "*********************",
        "qrcode": "https://usercontents.authing.cn/wxapp/qrcode/SweuVjfoPwSUTVEUv.png",
        "expiredAt": "2018-07-16T12:56:03.000Z",
        "__v": 0,
        "createdAt": "2018-07-16T12:55:03.302Z",
        "redirect": "",
        "success": false,
        "used": false
    },
    "code": 200
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

* 返回数据中 data 中的 QRCode 即二维码地址，可直接先客户端显示。
* 若处理成功，code 为 200，非 200 都为失败。

{% api-method method="post" host="https://oauth.authing.cn" path="/oauth/wxapp/confirm/qr?random=RANDOM\_STRING" %}
{% api-method-summary %}
轮询查询扫码状态
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    "data": {
        "code": 200,
        "message": "扫码登录成功",
        "data": {
            "_id": "*********************",
            "email": null,
            "emailVerified": false,
            "username": "ivy",
            "nickname": "ivy",
            "company": "",
            "photo": "https://wx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTLkQc7PfrbBqFMib6lkPUxaA5UsMiadibfWQtKv0CBcKnH2khXicvUB9WB2ibYxN6GRTaTsQfPtlsAafBg/132",
            "browser": "",
            "token": "******************************************.*********************.*********************",
            "tokenExpiredAt": "Wed Aug 01 2018 15:59:42 GMT+0800 (CST)",
            "loginsCount": 14,
            "lastLogin": "Tue Jul 17 2018 15:59:42 GMT+0800 (CST)",
            "lastIP": "*********************",
            "signedUp": "Tue Jul 17 2018 11:15:03 GMT+0800 (CST)",
            "blocked": false,
            "isDeleted": false,
            "__typename": "ExtendUser"
        },
        "redirect": "http://sample.authing.cn/#/redirect"
    },
    "code": 200
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

* `redirect` 为用户在 Authing 控制台中配置的回调地址，开发者若有需要可自行回调到此地址
* 如果用户已扫码，则 code 为 200，若为非 200，则代表用户未扫码或扫码失败

## 私有部署小程序后的使用方法

本章节针对将小程序部署到自己主体下的用户，请点击以下链接查看：

{% page-ref page="private-wxapp.md" %}

## 接下来你可能还需要

{% page-ref page="../../sdk/sdk-for-web/" %}

