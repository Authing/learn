# 支付宝登录

{% hint style="info" %}
此社会化登录方式提供 GitHub Demo：[https://github.com/authing/AuthingIOSDemo](https://github.com/authing/AuthingIOSDemo)
{% endhint %}

示例 Demo:

![](../../.gitbook/assets/3.gif)



## 准备工作 <a id="prepare"></a>

你一共需要准备以下内容：

1. 注册一个 [Authing 开发者账号](../../quickstart/create-authing-account.md)
2. 申请一个注册支付宝移动应用
3. 在 Authing 控制台填入支付宝移动应用信息

### 申请注册支付宝移动应用

前往 [支付宝开放平台](https://openhome.alipay.com/) 注册支付宝移动应用，你需要按照[此页面](https://docs.open.alipay.com/291/105971/)的指引生成一对 RSA 密钥对，私钥自己妥善保存，将公钥提交给支付宝换取支付宝公钥。

![](../../.gitbook/assets/image%20%28123%29.png)

{% hint style="info" %}
请确保开启了 “获取会员信息” 功能。
{% endhint %}

### 在 Authing 控制台填入支付宝移动应用信息

在 Authing 控制台 **用户池** -&gt; **第三方登录** -&gt; **社会化登录** 页面找到“移动端社会化登录“中的“支付宝移动应用“：

![](../../.gitbook/assets/zhi-fu-bao.png)

在此填入你的支付宝移动应用信息：

* AppID（应用 ID）：在 [管理中心 - 我的应用列表](https://openhome.alipay.com/platform/appManage.htm#/apps) 可以获取到。
* AlipayPID（合作伙伴账号 PID）：在 [账号中心 - 合作伙伴管理](https://openhome.alipay.com/platform/accountSetting.htm) 页面可以获取到。
* AppPrivateKey（应用私钥）：上一步生成的应用私钥。

![](../../.gitbook/assets/image%20%28486%29.png)

## 正式接入

### 接入 AlipaySDK-iOS

请按照[支付宝客户端 iOS 集成流程](https://docs.open.alipay.com/204/105295/)的指引，如果遇到问题，这里提供一个 Swift Demo App 供开发者参考：[https://github.com/authing/AuthingIOSDemo](https://github.com/authing/AuthingIOSDemo) .

### 获取 authInfo

拉起支付宝需要先在服务器获取生成 authInfo，开发者可以调用我们的接口：

{% api-method method="get" host="https://oauth.authing.cn/" path="oauth/alipaymobile/authinfo/:userPoolId" %}
{% api-method-summary %}

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
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    "code": 200,
    "message": "",
    "data": "apiname=com.alipay.account.auth&app_id=2018010901722731&app_name=mc&auth_type=AUTHACCOUNT&biz_type=openservice&method=alipay.open.auth.sdk.code.get&pid=2088422236285260&product_id=APP_FAST_LOGIN&scope=kuaijie&sign_type=RSA2&target_id=8a7a4ac1-888f-4e73-93d8-82bdc5786cec&sign=inNShZI0RrLvR%2F3V10tAQOQTLSmUKNtzbXbmDKVKGXc%2FCeIhoGqv4hrD4amq4h8ejj9PwAJkToXxLZI%2FSNQDcABFsrPGagKMpIn7wOBBYKHguOt5gJmG0D305eehmXheosDu4GmhPPpDe3Lks4aWjwswbN2rD2KqqGKOrEtTrXIomc%2By30F37E2rH8jwo7zeATjY%2B27qbgu%2FlGkHg0SwZ1GHs%2Fxic0xRMilxDRCd%2BSLYuyQhqBO%2B6lKXA%2BZb8N1V5GHWLkC1rpNEKPgfXwU6lWaXJ6JfFKIshPsJYEXRbTtIDUqI5sd8o8Y68MlYf1OlsOKmz24%2FZRKm%2Bu2JpVyWpw%3D%3D"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

swift 代码示例：

```swift
import Alamofire

func convertToDictionary(text: String) -> [String: Any]? {
    if let data = text.data(using: .utf8) {
        do {
            return try JSONSerialization.jsonObject(with: data, options: []) as? [String: Any]
        } catch {
            print(error.localizedDescription)
        }
    }
    return nil
}

// 获取 authInfo
AF.request(GetAuthInfoUrl).responseString { response in
    let resp = convertToDictionary(text: response.value!)!
    
    // Authing 业务状态码, 200 表示成功
    let code = resp["code"]! as! Int
    let message = resp["message"]! as! String
    if code == 200 {
        let authInfo = resp["data"]! as! String
        debugPrint("authInfo: \(authInfo)")
    } else {
        debugPrint("Message: ", message)
    }
}
```

### 打开支付宝登录

Swift 示例代码：

```swift
// 打开支付宝
AlipaySDK().auth_V2(withInfo: authInfo, fromScheme: "authing-alipay", callback: { resp in
    debugPrint(resp)
})
```

{% hint style="info" %}
这里的 fromSchema 需要和设置的 URL Types 中的 URL Schema 一致：如下图所示：
{% endhint %}

![](../../.gitbook/assets/image%20%28497%29.png)

### 接收支付宝回调数据

你可以在以下方法中获取到支付宝回调数据（详情见[此 Demo](https://github.com/authing/AuthingIOSDemo)）：

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>)
```

Swift 示例代码：

```swift
func parseUrlQueryString(string: String) -> [String:Any] {
    let arr = string.components(separatedBy:"&")
    var data = [String:Any]()
    for row in arr {
        let pairs = row.components(separatedBy:"=")
        data[pairs[0]] = pairs[1]
    }
    return data
}


// schema 回调
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
    let url = URLContexts.first?.url
    let schema = url!.scheme
    switch schema {
    case AlipayURLSchema:
        AlipaySDK().processAuth_V2Result(url, standbyCallback: { back in
            let response = back!
            let resultStatus = response["resultStatus"] as! String
            let memo = response["memo"]
            let result = response["result"] as! String
            
            let data = parseUrlQueryString(string: result)
            let success = data["success"]
            let result_code = data["result_code"] as! String
            let auth_code = data["auth_code"] as! String
            let user_id = data["user_id"] as! String
            
            if resultStatus == "9000" && result_code == "200" {
                // 表示授权成功
            }
        })
    default:
        debugPrint("???")
    }
}
```

### 换取用户信息

在上一步获取到 auth\_code 可通过下面的 API 换取用户信息：

{% api-method method="post" host="https://oauth.authing.cn/" path="oauth/alipaymobile/redirect/:userPoolId" %}
{% api-method-summary %}

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
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
  emailVerified: false,
  username: '廖长江',
  nickname: '廖长江',
  company: '',
  photo: 'https://usercontents.authing.cn/avatar-5e53ee0684197a42d319628b-1582558729878',
  loginsCount: 28,
  registerMethod: 'oauth:wechatmobile',
  blocked: false,
  isDeleted: false,
  oauth: '{"openid":"osJ4Ys7kderNmGP7qgjIiNSLPm94","nickname":"廖长江","sex":1,"language":"en","city":"海淀","province":"北京","country":"中国","headimgurl":"http://thirdwx.qlogo.cn/mmopen/vi_32/Z6XlHHTohpQQMqUjDsWibCaRH6RZiafrbibBEVzffMp7Co2kGbUmfo9ln80tOPYv9RRGJIvhv7OKrwncssxOod3xQ/132","privilege":[],"unionid":"o0pqE6Fbr5M-exSu_PeL_sjwN44U"}',
  metadata: '""',
  _id: "5e53ee0684197a42d319628b",
  unionid: 'o0pqE6Fbr5M-exSu_PeL_sjwN44U',
  openid: 'osJ4Ys7kderNmGP7qgjIiNSLPm94',
  lastIP: '::ffff:192.168.0.106',
  lastLogin: "2020-02-26T02:53:56.223Z",
  signedUp: "2020-02-24T15:38:46.620Z",
  token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7InVuaW9uaWQiOiJvMHBxRTZGYnI1TS1leFN1X1BlTF9zandONDRVIiwiaWQiOiI1ZTUzZWUwNjg0MTk3YTQyZDMxOTYyOGIiLCJjbGllbnRJZCI6IjVlNGNkZDA1NWRmM2RmNjVkYzU4Yjk3ZCJ9LCJpYXQiOjE1ODI2ODU2MzYsImV4cCI6MTU4Mzk4MTYzNn0.MHo07ilZl8ovur_B2iSIQ0wSHyMsPP7EOgREVVfPoGM',
  tokenExpiredAt: "2020-03-12T02:53:56.000Z",
  phone: null
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

Swift 代码示例：

```swift
func convertToDictionary(text: String) -> [String: Any]? {
    if let data = text.data(using: .utf8) {
        do {
            return try JSONSerialization.jsonObject(with: data, options: []) as? [String: Any]
        } catch {
            print(error.localizedDescription)
        }
    }
    return nil
}

let url = "https://oauth.authing.cn/oauth/alipaymobile/redirect/\(UserPoolId)"
struct Body: Encodable {
    let auth_code: String
}
let body = Body(auth_code: auth_code)
AF.request(
    url,
    method: .post,
    parameters: body,
    encoder: JSONParameterEncoder.default
).responseString { response in
    
    // 将 response.value 转化成字符串，示例如下：
    // ["code": "200", "message": "获取用户信息成功", data: "" ]
    let resp = convertToDictionary(text: response.value!)!
    
    let data = resp["data"] as! [String: Any]
    debugPrint("Data: \(data)")
}
```

## 完成接入

恭喜你，此时已经接入了支付宝移动应用登录。获取到用户信息之后，你可以得到登录凭证 token，你可以在后续的 API 请求中携带上此 token, 然后在后端接口中根据此 token 区分不同用户，详情请见[验证 token](../../advanced/verify-jwt-token.md#yan-zheng-authing-qian-fa-de-token)。

我们还提供了 React Native SDK，可以让你在 React Native 快速接入支付宝登录：

{% page-ref page="../../sdk/sdk-for-react-native.md" %}

