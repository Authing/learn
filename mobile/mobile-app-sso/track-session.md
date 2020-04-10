# 自动检测登录

## 准备工作

1. [注册 Authing 开发者账号](../../quickstart/create-authing-account.md)

## 原理介绍

自动检测同一设备上关联应用的登录态，本质上就是建立起一个 deviceId （设备 ID） 与 Authing 服务器之间的 session 连接。

当某个用户在某个应用上登录后，调用 Authing 接口创建一个 deviceId 与 Authing 服务器之间的 session，这样用户在同一设备的其他应用登录的时候，就能检测到此 session 的存在，从而跳过登录步骤，实现自动登录。

假设你有三个 App: App 1、App2 和 App3，只要有其中一个 App 和 Authing 服务器建立了 session 关系，就可以检测到 session。

## 开始接入

### 获取设备 ID

{% hint style="warning" %}
请在测试时务必验证自己在不同的 App 内获取到的 deviceId 是一致的！
{% endhint %}

#### iOS

iOS 设备的设备 ID 可以通过 [identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) 获取，同一个 vendor 的应用获取到的设备 ID 是一样的。

{% hint style="info" %}
什么情况下属于同一个 vendor？

1. 从 App Store 下载的应用，基于在 App Store 登记的应用信息判断。
2. 非 App Store 下载的应用
   1. iOS 6 及之前，bundle id 的前两部分一致的应用属于同一 vendor，如 com.example.app1和 com.example.app2 为同一 vendor。com.example.app1.xxx 和 com.example.app2.xxx 也属于同一 vendor。
   2. iOS 7 及之后，bundle id 的除了最后一部分的其余部分一致的应用属于同一 vendor，如 com.example.app1和 com.example.app2 为同一 vendor。但是 com.example.app1.xxx 和 com.example.app2.xxx 不属于同一 vendor。
{% endhint %}

如果你的应用不属于同一 vendor，建议使用 [ASIdentifierManager](https://developer.apple.com/documentation/adsupport/asidentifiermanager)。

Swift 5 代码示例：

```swift
let deviceId = UIDevice.current.identifierForVendor!.uuidString
```

OC 代码示例：

```objectivec
UIDevice *currentDevice = [UIDevice currentDevice];
NSString *deviceId = [[currentDevice identifierForVendor] UUIDString];
```

#### Android

Android 设备可通过 [ANDROID\_ID](https://developer.android.com/reference/android/provider/Settings.Secure.html#ANDROID_ID) 获取：

Java 代码示例：

```java
import android.provider.Settings.Secure;
private String android_id = Secure.getString(getContext().getContentResolver(),
                                                        Secure.ANDROID_ID); 
```

Kotlin 代码示例：

```kotlin
val deviceID = Settings.Secure.getString(contentResolver,
Settings.Secure.ANDROID_ID)
```

{% api-method method="post" host="https://core.authing.cn" path="/oauth/sso/mobile/createSession" %}
{% api-method-summary %}
创建 session
{% endapi-method-summary %}

{% api-method-description %}
此接口用于在一个移动应用客户端内创建一个 session ，**且用户需处于登录状态**，在请求头中加上 authorization 请求头携带用户 token。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="authorization" type="string" required=true %}
登录用户的 token
{% endapi-method-parameter %}

{% api-method-parameter name="content-type" type="string" required=true %}
application/json
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="appId" type="string" required=true %}
移动应用 ID，任意字符串，请确保在你的应用内保持唯一。
{% endapi-method-parameter %}

{% api-method-parameter name="deviceId" type="string" required=true %}
设备 ID
{% endapi-method-parameter %}

{% api-method-parameter name="userPoolId" type="string" required=true %}
用户池 ID
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    code: 200,
    message: "创建 session 成功!",
    data: {
        sessionId: "xxxxxx", // session ID
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}



Swift 代码示例：

```swift
func createSession(userPoolId: String, token: String){
    // 移动端 SSO: createSession
    struct MobileSSO: Encodable {
        let userPoolId: String
        let deviceId: String
        let appId: String
    }
    let body = MobileSSO(
        userPoolId: UserPoolId, 
        deviceId: UIDevice.current.identifierForVendor!.uuidString,
        appId: "app1"
    )
    let headers: HTTPHeaders = [
        "Authorization": token ,
        "Accept": "application/json"
    ]
    let api = "https://core.authing.cn/oauth/sso/mobile/createSession"
    AF.request(api, method: .post, parameters: body, encoder: JSONParameterEncoder.default, headers: headers).response { response in
         debugPrint(response)
    }
}

```

{% api-method method="get" host="https://core.authing.cn" path="/oauth/sso/mobile/trackSession" %}
{% api-method-summary %}
查询 session
{% endapi-method-summary %}

{% api-method-description %}
此接口用于在移动应用客户端内查询 session，不需要用户处于登录态。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="content-type" type="string" required=true %}
application/json
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="deviceId" type="string" required=true %}
设备 ID
{% endapi-method-parameter %}

{% api-method-parameter name="userPoolId" type="string" required=true %}
用户池 ID
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
分两种情况：直**接返回用户信息**和**返回 ticket**
{% endapi-method-response-example-description %}

```javascript
// 直接返回用户信息
{
    code: 200,
    message: '获取 session 用户信息成功',
    data: {
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

// 返回 ticket
{
    code: 200,
    message: '获取 session 用户信息成功',
    data: {
      ticket: "xxxxdjdkxxxxx",
      nickname: "xxxx",
      photo: "https://usercontents.authing.co/authing-avatar.png"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

Authing  trackSession 返回的数据支持两种形式（你可以在  Authing 控制台**基础配置** - **基础设置** 页面进行修改）：

1. 直接返回完整用户信息，包含 token
2. 返回昵称头像（用于展示目的）以及用于换取用户信息的 ticket（默认方式）

{% hint style="info" %}
推荐使用第二种方式，因为第一种方式存在安全风险，存在第三方非法批量模拟设备 ID 查询 session 的可能。使用 ticket 要求你在后端使用用户池密钥换取用户信息（详情见下文），保证了用户信息不会被非法获取。
{% endhint %}

如果你采用第二种方式，可以在前端展示用户昵称和头像，如下图所示：

![](../../.gitbook/assets/image%20%28461%29.png)

{% api-method method="post" host="https://core.authing.cn" path="/oauth/sso/mobile/exchangeUserInfoWithTicket" %}
{% api-method-summary %}
使用 ticket 换取用户信息
{% endapi-method-summary %}

{% api-method-description %}
使用 ticket 换取用户信息，**此接口需要用户池密钥，请在后端调用**！
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="content-type" type="string" required=true %}
application/json
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="ticket" type="string" required=true %}
trackSession 获取的 ticket
{% endapi-method-parameter %}

{% api-method-parameter name="secret" type="string" required=true %}
用户池密钥
{% endapi-method-parameter %}

{% api-method-parameter name="userPoolId" type="string" required=true %}
用户池 ID
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
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
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}



{% api-method method="post" host="https://core.authing.cn" path="/oauth/sso/mobile/destorySession" %}
{% api-method-summary %}
 销毁 session
{% endapi-method-summary %}

{% api-method-description %}
此接口用于在一个移动应用客户端内销毁一个 session ，**且用户需处于登录状态，在请求头中加上 authorization 请求头携带用户 token**。由于存在多个应用，所以默认情况下只会销毁制定 App 的 session（trackSession 只要还有一个 App 有 session 就会查询到 session），如果你想清除所有 App 的 session，可以设置 destoryAll  为 true。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="authorization" type="string" required=true %}
登录用户的 token
{% endapi-method-parameter %}

{% api-method-parameter name="content-type" type="string" required=true %}
application/json
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="destoryAll" type="boolean" required=false %}
是否销毁同一设备上所有 App 的 session，默认为 false
{% endapi-method-parameter %}

{% api-method-parameter name="appId" type="string" required=true %}
移动应用 ID，任意字符串，请确保在你的应用内保持唯一。
{% endapi-method-parameter %}

{% api-method-parameter name="deviceId" type="string" required=true %}
设备 ID
{% endapi-method-parameter %}

{% api-method-parameter name="userPoolId" type="string" required=true %}
用户池 ID
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    code: 200,
    message: "销毁 session 成功!"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
你应该在每次用户退出登录以及删除 App 的时候调用此接口。
{% endhint %}

