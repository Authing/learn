# APP 拉起小程序登录

{% hint style="success" %}
「移动应用小程序登录」是指在移动应用中，借助微信开放能力，唤起微信小程序「小登录」，获取用户微信授权进行登录。**此方法可以获取到用户手机号**。
{% endhint %}

{% hint style="info" %}
此登录方式提供 GitHub Demo ：[https://github.com/authing/AuthingIOSDemo](https://github.com/authing/AuthingIOSDemo)
{% endhint %}

示例 Demo：

![](../../.gitbook/assets/2.gif)

你可以扫描下方的小程序二维码体验一下「小登录」：

![](../../.gitbook/assets/image%20%28327%29.png)



{% hint style="info" %}
如果你想私有化部署自己的小程序，可以联系我们。手机号：[18616699123](tel:18616699123)。
{% endhint %}

## 准备工作

1. Authing 开发者账号
2. [申请微信移动应用](https://open.weixin.qq.com/cgi-bin/frame?t=home/app_tmpl&lang=zh_CN)
3. 移动应用关联小登录
4. 在 Authing 后台填入你的小程序信息（需要私有化部署的开发者填）

### 移动应用关联小登录

可在[微信开放平台](https://open.weixin.qq.com/) **“管理中心-移动应用-应用详情-关联小程序信息”**，为通过审核的移动应用发起关联小程序操作。小登录的 AppID 为 wxa0435021fd7a3af2 .

![](../../.gitbook/assets/image%20%28354%29.png)

### 在 Authing 控制台配置小程序信息（可选，私有化部署需填）

在 Authing 控制台 **用户池** -&gt; **第三方登录** -&gt; **社会化登录** 页面找到“移动端社会化登录“中的“移动应用小程序登录“：

![](../../.gitbook/assets/image%20%2824%29.png)

填入你的小程序的 AppID 和 AppSecret:

![](../../.gitbook/assets/image%20%28176%29.png)

### 自定义配置项

Authing 一直以来都致力于带给开发者高度自定义的开发体验，所以我们提供以下自定义配置项，开发者可以根据自己业务的需要，在安全性和便捷性之间权衡。

开发者可在 [Authing 控制台](https://authing.cn/dashboard) **基础配置** -&gt; **基础设置** -&gt; **App 打开小程序登录自定义配置项** 处自定义配置。

![](../../.gitbook/assets/image%20%28476%29.png)

#### 换取用户信息是否需要用户池 secret

默认为 true。如果开启，使用 ticket 换取用户信息时必须带上用户池 secret，这意味着你不能直接在 App 内进行用户信息换取操作，必须在服务端完成。

#### ticket 有效时间

默认为 120 秒。

## 接入流程（以 Swift 为例）

### 在 App 内发起拉起小程序请求

开发者需要调用 WechatOpenSDK（具体的接入流程请见[微信官方文档](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Access_Guide/iOS.html)） 唤起小程序：

* 小程序 userName 为 gh\_a781a791e29e
* path 为 /routers/explore
* path query 参数为：
  * userPoolId: 你的用户池 ID
  * getPhone: 是否需要获取用户手机号，1 表示需要，不填或者其他表示不需要
  * fromApp: 1，此参数必填。
  * useSelfWxapp: 0。此参数必填。

以 swift 语言为例：

```swift
let userPoolId = "5e4cdd055df3df65dc58b97d"
let req = WXLaunchMiniProgramReq()
req.userName = "gh_a781a791e29e"
req.path = "/routes/explore?userPoolId=\(userPoolId)&getPhone=1&fromApp=1&useSelfWxapp=0"
req.miniProgramType = WXMiniProgramType.test
WXApi.send(req)
```

### 接收小程序返回的数据

小登录会返回一串 URL Query String 给 App，开发者可以将其转换成一个字典：

* code: 业务状态码, 200 表示成功。
* message: 提示信息。
* ticket: 授权成功时带有。开发者可用此获取用户信息

```swift
func onResp(_ resp: BaseResp) {
    print("onResp: type=\(resp.type)")
    if resp.isKind(of: WXLaunchMiniProgramResp.self ) {
        if resp.errCode == 0 && resp.type == 0{//授权成功
            let response = resp as! WXLaunchMiniProgramResp
            let extMsg = response.extMsg!
            //  微信登录成功通知
            debugPrint(extMsg)
            let arr = extMsg.components(separatedBy:"&")
            var data = [String:Any]()
            for row in arr {
                let pairs = row.components(separatedBy:"=")
                data[pairs[0]] = pairs[1]
            }
            let code = data["code"]
            let message = data["message"]
            let ticket = data["ticket"]
            debugPrint(code, message, ticket)
        }
    }
}
```

### 获取用户信息

用户同意授权的情况下，开发者可以使用 ticket 换取用户信息，ticket 默认有效时间为两分钟。**默认情况下，出于安全考虑，ticket 换取用户信息需要提供用户池密钥，**这意味着不能直接在 App 内直接换取用户信息。我们同时也提供给开发者选择的权利，开发者可以在用户池基础配置页面修改此选项（见上文）。

{% api-method method="post" host="https://oauth.authing.cn/" path="oauth/app2wxapp/auth/:userPoolId" %}
{% api-method-summary %}
 ticket 换取用户信息
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="userPoolId" type="string" required=true %}
用户池 ID
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-headers %}
{% api-method-parameter name="content-type" type="string" required=true %}
application/json
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="secret" type="string" required=false %}
用户池密钥
{% endapi-method-parameter %}

{% api-method-parameter name="tickett" type="string" required=true %}
小程序返回给 App 的ticket
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
\_id 为用户 ID。
{% endapi-method-response-example-description %}

```javascript
{
  emailVerified: false,
  username: '廖长江',
  nickname: '廖长江',
  company: '',
  photo: 'https://usercontents.authing.cn/avatar-5e57540f84622ae263294a42-1582781455906',
  loginsCount: 2,
  registerMethod: 'oauth:app2wxapp',
  blocked: false,
  isDeleted: false,
  oauth: '{"nickName":"廖长江","gender":1,"language":"zh_CN","city":"Haidian","province":"Beijing","country":"China","avatarUrl":"https://wx.qlogo.cn/mmopen/vi_32/8INxh2bxDMiaU05jLqvWBszALu2u8Qw4iaxV58v4fERaDWV8yunE7icNiahJdxkOCNfGosqXcQ2SyScAcdyibv8uWWQ/132","phone":"17670416754","openid":"o1p9H4wAgb9uTqpxG5Z1g0pIr3FE","unionid":"o0pqE6Fbr5M-exSu_PeL_sjwN44U"}',
  metadata: '""',
  _id: "5e57540f84622ae263294a42",
  unionid: 'o0pqE6Fbr5M-exSu_PeL_sjwN44U',
  openid: 'o1p9H4wAgb9uTqpxG5Z1g0pIr3FE',
  lastIP: '::ffff:192.168.0.106',
  lastLogin: "2020-02-27T05:33:20.796Z",
  signedUp: "2020-02-27T05:30:55.875Z",
  token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7InVuaW9uaWQiOiJvMHBxRTZGYnI1TS1leFN1X1BlTF9zandONDRVIiwiaWQiOiI1ZTU3NTQwZjg0NjIyYWUyNjMyOTRhNDIiLCJjbGllbnRJZCI6IjVlNGNkZDA1NWRmM2RmNjVkYzU4Yjk3ZCJ9LCJpYXQiOjE1ODI3ODE2MDAsImV4cCI6MTU4NDA3NzYwMH0.pd7HJu5Ft8uytxIy4VgHdQiaAKuo96P_LcABFwM7OPI',
  tokenExpiredAt: "2020-03-13T05:33:20.000Z",
  phone: '176xxxx6754'
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

swift 代码示例：

```swift
import Alamofire

struct Body: Encodable {
    let ticket: String
}
let body = Body(ticket: ticket)
let url = "http://192.168.0.103:5510/oauth/app2wxapp/auth/5e4cdd055df3df65dc58b97d?ticket=\(ticket)"
AF.request(
    url,
    method: .post,
    parameters: body,
    encoder: JSONParameterEncoder.default
).responseJSON { response in
    debugPrint("Response: \(response)")
}
```

## 完成接入

恭喜你，此时已经接入了微信移动应用登录。获取到用户信息之后，你可以得到登录凭证 token，你可以在后续的 API 请求中携带上此 token, 然后在后端接口中根据此 token 区分不同用户，详情请见[验证 token](../../advanced/verify-jwt-token.md#yan-zheng-authing-qian-fa-de-token)。



