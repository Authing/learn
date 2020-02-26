---
description: >-
  APP 扫码登录是指在移动设备上扫描二维码登录 Web 网站，在 Web，Authing 还提供了相关 SDK
  接口，可以帮助你快速实现一个完整的扫码登录组件。
---

# 快速接入

## 代码示例

Web 端：[https://github.com/Authing/app-qrcode-login-sample](https://github.com/Authing/app-qrcode-login-sample)

APP 端：

## 在线体验

点击在线体验：https://sample.authing.co/app-qrcode/

## 接入流程

### Web 端

在 Web 端，我们推荐使用Authing 提供的 SDK。安装流程请查看 SDK for JavaScript。

{% page-ref page="../../sdk/sdk-for-javascript/" %}

安装完成后，可以新建一个 Web 项目，然后复制以下代码：

```javascript
const Authing = require('authing-js-sdk');

// 初始化 Authing SDK for JavaScript
const authing = new Authing({
    userPoolId: 'your_userpool_id',
});

// 调用 APP 扫码登录的方法，此方法将生成一个用于扫码登录的图片和相关提示信息
authing.startAppAuthScanning({
  onSuccess(data) {
    alert('扫码成功，请打开控制台查看用户信息')
    console.log(data);
    const {ticket, userInfo} = data;
    // 存储 token 到 localStorage 中
    localStorage.setItem('token', userInfo.token);
  }
})
```

运行后将自动生成用于 APP 扫码登录的二维码：

![](../../.gitbook/assets/image%20%28467%29.png)

扫码成功之后，Authing 将会回调开发者传入的 onSuccess，回调的参数中包含了 ticket 和 userInfo，ticket 可以用来换取用户信息。

如果你想自定义 UI ，了解如何使用 ticket 换取用户信息，可以查看：

{% page-ref page="web-side-sdk.md" %}

{% page-ref page="full-api-list.md" %}

### 移动端

Authing 生成的二维码中包含的原始信息为一串字符串，转换为 JSON 后如下：

```javascript
{
    "scene": "APP_AUTH",
    "qrcodeId": "5e05f0c57fde537d950f7da5",
    "userPoolId": "5e04ae0d5f3cee22fb37612b",
    "createdAt": "2019-12-27T11:53:41.260Z",
    "expireAt": "2019-12-27T11:55:41.260Z",
    "userDefinedData": { "hello": "world" }
}
```

字段含义如下：

* scene：场景值，APP\_AUTH 表示 APP 扫码登录。
* qrcodeId：二维码 ID，移动端之后根据此 ID 完成确认扫码、同意授权、取消授权（注意，这里的“确认扫码”意思是移动端标记此二维码已经被扫描，但是用户还没有采取同意或取消操作。有关二维码的详细状态，请见完整接口列表页）
* userPoolId：用户池 ID。
* createdAt：二维码创建时间。
* expireAt：二维码过期时间。
* userDefinedData：用户自定义字段。了解如何添加自定义数据，请见完整接口列表页。

> 有关如何在 IOS 中扫描并解析二维码的内容，可以查看[这篇文章](https://github.com/darkjoin/Learning/wiki/使用AVFoundation读取二维码)。

要实现 APP 扫描登录 Web 端，首先要求 APP 端用户处于登录状态（这是理所当然的），调用相关接口的时候要带上终端用户的 token。移动端一共需要用到三个接口：

* 确认扫码
* 同意授权
* 取消授权

> 要了解这个三个接口的详情，请见完整接口列表页。

下面以 Objective-C 为例，实现同意授权登录：

* api 地址为：[http://oauth.athing.co/oauth/scan-qrcode/confirm](http://oauth.athing.co/oauth/scan-qrcode/confirm)
* 第 9 行在请求头带上了用户登录凭证。

```objectivec
- (void) ConfirmAuthorization:(NSString *) qrcodeId{    
    NSURL * api =[NSURL URLWithString:@"http://oauth.athing.co/oauth/scan-qrcode/confirm"];
    NSDictionary *bodyDict = @{
        @"qrcodeId": qrcodeId,
    };
    NSData *body = [NSJSONSerialization dataWithJSONObject:bodyDict options:kNilOptions error:nil];
    NSMutableURLRequest *request = [[NSMutableURLRequest alloc] initWithURL:api];
    request.HTTPMethod = @"POST";
    [request setValue:self.USER_TOKEN forHTTPHeaderField:@"Authorization"];
    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request setValue:@"application/json" forHTTPHeaderField:@"Accept"];
    [request setHTTPBody:body];
    
    NSURLSessionConfiguration *config = [NSURLSessionConfiguration defaultSessionConfiguration];
    NSURLSession *session = [NSURLSession sessionWithConfiguration:config];
    NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
        NSHTTPURLResponse *httpResponse = (NSHTTPURLResponse *)response;
        if(httpResponse.statusCode == 200)
        {
            NSError *parseError = nil;
            NSDictionary *responseDictionary = [NSJSONSerialization JSONObjectWithData:data options:0 error:&parseError];
            NSLog(@"The response is - %@",responseDictionary);
            NSInteger code = [[responseDictionary objectForKey:@"code"] integerValue];
            if(code == 200)
            {
                NSLog(@"SUCCESS");
            }
            else
            {
                NSLog(@"FAILURE");
            }
        }
        else
        {
            NSLog(@"Network Error");
        }
    }];
    
    [dataTask resume];
}
```

### 用户扫码成功后

移动端确认授权之后，Web 将会看到相关提示。

![](../../.gitbook/assets/image%20%28439%29.png)

这个时候，整个登录流程也就完成了，开发者可以使用 ticket 去换取用户信息了。

```objectivec
 authing.exchangeUserInfoWithTicket(ticket).then(res => {
    const { code } = res
    if (code === 200) {
      console.log("Exchange userInfo success: ", res)
    } else {
      console.log("Exchange userInfo failed: ", res)
    }
  })
```

返回结果示例：

```objectivec
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

其中有登录凭证 token，下面我们以一个具体的例子说明一下如何处理这个 token：



了解验证 token的详情，可查看：

{% page-ref page="../../advanced/verify-jwt-token.md" %}

