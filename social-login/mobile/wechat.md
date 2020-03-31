# 微信登录

{% hint style="info" %}
此社会化登录方式提供 GitHub Demo ：[https://github.com/authing/AuthingIOSDemo](https://github.com/authing/AuthingIOSDemo)
{% endhint %}

示例  Demo :

![](../../.gitbook/assets/output.gif)



## 准备工作 <a id="prepare"></a>

你一共需要准备以下内容：

1. Authing 开发者账号
2. [申请微信移动应用](https://open.weixin.qq.com/cgi-bin/frame?t=home/app_tmpl&lang=zh_CN)
3. 配置 iOS Universal Links \(针对 iOS 应用, 需开通 [Apple Developer 账号](https://developer.apple.com/)\)
4. 在 Authing 控制台填入微信移动应用信息

### 配置 iOS Universal Links

> 从微信 WechatOpenSDK 1.8.6 开始，iOS 移动应用需填写 Universal Links 信息。如果你是要开发 Android 应用或者已经配置好了，可跳过此节。[Apple 官方文档请见此](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/enabling_universal_links)。

#### 配置 apple-app-site-association 文件

在 Apple Developer 控制台 **Membership** 页面找到自己的 Team ID:

![](../../.gitbook/assets/image%20%28459%29.png)

在 Xcode  **Targets** -&gt; **Signing & Capabilities** 中找到 Bundle Identifier:

![](../../.gitbook/assets/image%20%28383%29.png)

接下来创建 apple-app-site-association 文件：

假设你的 Team ID 为 xxxxxxx, Bundle Identifier 为 com.example.exampleApp, 设置 Universal Link 的 Path 为`/native/*`, 则 apple-app-site-association 如下：

假设你的 Team ID 为 xxxxxxx, Bundle Identifier 为 com.example.exampleApp, 设置 Universal Link 的 Path 为`/native/*`, 则 apple-app-site-association 如下：

```text
{
	"applinks": {
		"apps": [],
		"details": [
			{
				"appIDs": [ "xxxxxxx.com.example.exampleApp" ],
				"paths": [ "/native/*" ]
			}
		]
	}
}
```

你需要将此文件部署到你的域名的 .well-known/apple-app-site-association 链接下，如你的域名为 example.com，则需要通过 https://example.com/.well-known/apple-app-site-association 访问到该文件。以下几点需要注意：

* **必须使用 https**
* apple-app-site-association 需要是一个合法的 JSON 文件，但是**没有 .json  后缀**。
* content-type 需要设置为 application/json
* paths 请**使用 \* 通配符**，微信要求。

以下是一个 nginx 配置示例：（这里将 apple-app-site-association 文件放与某个 .well-known 文件夹下）

```text
server {
  listen 80;
  listen 443 ssl;

  server_name authing.cn;

  ssl_certificate /mnt/cerm/client/1_authing.cn_bundle.crt;
  ssl_certificate_key /mnt/cerm/client/2_authing.cn.key;
  ssl_session_timeout 5m;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_prefer_server_ciphers on;


  location /.well-known {
    alias /path/to/your/.well-known/folder;
    try_files $uri $uri/ =404;
  }
}
```

#### 在 Xcode 中配置 Associated Domains

加下来在 Xcode 中配置 Associated Domains。 在 Xcode   **Targets** -&gt; **Signing & Capabilities** 页面点击右上角的 **+Capability** :

![](../../.gitbook/assets/image%20%28455%29.png)

选择 Associated Domains: 

![](../../.gitbook/assets/image%20%28113%29.png)

假设你的域名是 example.com, 则填入 `applinks:example.com` :

![](../../.gitbook/assets/image%20%28107%29.png)

#### 验证 Universal Links 是否生效

假设你的域名为 example.com，Path 为 `/native/*`在手机上安装了你的 App 之后，使用 Safari 浏览器访问 `https://example.com/native/`，网上拉动网页，你应该能看到你的 App: 

![](../../.gitbook/assets/image%20%28530%29.png)

再访问 `https://example.com/native/xxx`，依旧可以看到。

#### 在微信开放平台填写 Universal Links

![](../../.gitbook/assets/image%20%28196%29.png)

### 在 Authing 控制台配置微信移动应用信息

在 Authing 控制台 **用户池** -&gt; **第三方登录** -&gt; **社会化登录** 页面找到“移动端社会化登录“中的“微信移动应用“：

![](../../.gitbook/assets/image%20%28602%29.png)

填入你的移动应用 AppID 和 AppSecret: 

![](../../.gitbook/assets/image%20%28275%29.png)

AppID 和 AppSecret 可在微信开放平台 **管理中心** - **移动应用** - **应用详情** 页面看到：

![](../../.gitbook/assets/image%20%28548%29.png)

## 正式接入

### 接入 WechatOpenSDK

此部分请按照[微信官方文档](https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Access_Guide/iOS.html)指引接入 WechatOpenSDK，如果遇到问题，这里提供一个 Swift Demo App 供开发者参考：[https://github.com/authing/AuthingIOSDemo](https://github.com/authing/AuthingIOSDemo) .

### 发起登录请求

成功接入 SDK 之后，你应该能成功打开微信获取用户授权并获取到 authorization code：

下面是发起微信登录请求代码示例（ Swift）:

```swift
func loginByWechat() {
    let req = SendAuthReq()
    req.scope = "snsapi_userinfo" //获取用户信息
    req.state = "123" //随机值即可，这里用时间戳
    WXApi.send(req)
}
```

你可以在 AppDelegate 或 SceneDelegate 的 onResp 方法获取到授权码 code,  如下图所示：

![](../../.gitbook/assets/image%20%28374%29.png)

### 接收微信回调数据获取 code

下面是一个示例代码（Swift 语言）：

```swift
func onResp(_ resp: BaseResp) {
    
    debugPrint(resp)
    
    // 微信登录请求信息
    if resp.isKind(of: SendAuthResp.self) {
        if resp.errCode == 0 && resp.type == 0{
            let response = resp as! SendAuthResp
            
            // 微信 authorication_code
            let code = response.code
            debugPrint("code: " ,code)
        }
    }
}
```

### 换取用户信息

用户同意收取获取到 code 之后，需要调用 Authing 的接口获取用户信息：

{% api-method method="get" host="https://oauth.authing.cn" path="/oauth/wechatmobile/auth/:userPoolId" %}
{% api-method-summary %}
 
{% endapi-method-summary %}

{% api-method-description %}
使用 code 换取用户信息。
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="userPoolId" type="string" required=true %}
用户池 ID
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="country" type="string" required=false %}
微信返回给 APP 的 country
{% endapi-method-parameter %}

{% api-method-parameter name="lang" type="string" required=false %}
微信返回给 APP 的 lang
{% endapi-method-parameter %}

{% api-method-parameter name="state" type="string" required=false %}
微信返回给 APP 的 state
{% endapi-method-parameter %}

{% api-method-parameter name=" code" type="string" required=true %}
微信返回给 APP 的 code
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Authing 返回给开发者的用户信息是经过加工过后的 Authing 用户标准字段，非微信文档中说明的用户字段。
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

示例代码（Swift）:

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

let url = "https://oauth.authing.cn/oauth/wechatmobile/auth/\(UserPoolId)?code=\(code!)"
AF.request(url).responseString { response in
    let resp = convertToDictionary(text: response.value!)!
    
    // Authing 业务状态码, 200 表示成功
    let code = resp["code"]! as! Int
    let message = resp["message"]! as! String
    if code == 200 {
        let dataStr = resp["data"]! as! String
        let data = convertToDictionary(text: dataStr)!
        debugPrint("Data: \(data)")
        updateUserInfoEnvVariable(data: data)
    } else {
        debugPrint("Message: ", message)
    }
}
```

## 完成接入

恭喜你，此时已经接入了微信移动应用登录。获取到用户信息之后，你可以得到登录凭证 token，你可以在后续的 API 请求中携带上此 token, 然后在后端接口中根据此 token 区分不同用户，详情请见[验证 token](../../advanced/verify-jwt-token.md#yan-zheng-authing-qian-fa-de-token)。

