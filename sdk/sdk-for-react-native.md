# SDK for React Native

#### 🏠 [Homepage](https://github.com/Authing/authing-rn-sdk)

Authing 支持 React Native 移动端开发移动端，使得开发者可以快速接入支付宝、微信等 APP 登录，以及免代码开发支持邮箱密码、用户名密码、手机验证码登录。

![authing-rn-sdk &#x793A;&#x4F8B;](http://lcjim-img.oss-cn-beijing.aliyuncs.com/2019-12-03-150521.jpg)

[点击此查看演示视频](https://cdn.authing.cn/authing-rn-sdk.mp4)。

以下是提供的完整功能列表：

* 邮箱密码登录注册
* 手机验证码登录
* 用户名密码登录
* 忘记密码以及重置密码
* 移动端 APP 社会化登录

目前支持的社会化登录有：

* [x] 支付宝
* [ ] 微信

你可以通过下面这些文档了解如何使用 authing-rn-sdk 接入移动端社会化登录：

{% page-ref page="../advanced/social-login/jie-ru-yi-dong-duan-zhi-fu-bao-deng-lu.md" %}

如果你想直接调用接口，可以使用 [authing.js](https://github.com/Authing/authing.js%20%20
)。

### 安装

> 注：authing-rn-sdk 的 npm 包名称为 @authing/rn

```text
yarn add react-native-gesture-handler react-native-webview
yarn add @authing/rn
```

如果是 IOS，需要执行：

```text
cd ios && pod install
```

> 注：从 react-native 0.60 版本开始，不再需要手动执行 react-native link 指令。

如果一切顺利，你会看到：

![](../.gitbook/assets/image%20%28148%29.png)

> 由于平台限制，如果需要接入社会化登录，还需要进行一些额外配置，详情见下文。

现在一切就绪，可以开始使用 authing-rn-sdk 快速接入 Authing 强大的身份解决方案啦！

### 快速接入

接入 Guard 非常简单，最简情况下，你只需要指定应用池 ID 和成功登录事件的回调函数即可！（完整的事件列表见下文）

> 如果你对 Authing 用户池的概念不是很了解，可以先阅读基础概念文档。用户池 ID 可从 Authing 控制台中获取。

```javascript
import { Guard } from "@authing/rn"
```

```javascript
const onLogin = (userInfo) => {
    // deal with userInfo
}
```

```javascript
<Guard
  userPoolId={userPoolId}
  onLogin={onLogin}
 />
```

下面是一个简单的完整示例：

```javascript
import React from 'react';
import {
  SafeAreaView,
  StatusBar,
} from 'react-native';
import { Guard } from "@authing/rn"

const App = () => {
  const userPoolId = "5dd77e6efa26f000d18101ca"
  const options = {
    title: "Authing Guard SDK",
    forceLogin: true // 将注册和登录合并，当用户不存在的时候为其自动注册
  }
  const onLogin = (loginMethod, userInfo) => {
    alert(JSON.stringify(userInfo))
  }
  return (
    <>
      <StatusBar barStyle="dark-content" />
      <SafeAreaView style={{ flex: 1 }}>
        <Guard
          userPoolId={userPoolId}
          options={options}
          onLogin={onLogin}
        />
      </SafeAreaView>
    </>
  );
};

export default App;
```

通过以下指令运行： IOS：

```text
npx react-native run-ios
```

Android:

```text
npx react-native run-android
```

用户成功登录之后 authing-rn-sdk 会将用户信息 `userInfo` 回调给传入的 `onLogin` 函数，用户信息中包含了 Authing 用户 ID、头像、昵称等，还包括登录凭证 `token`。`userInfo` 示例如下：

```javascript
{
    "_id": "5dc10bcb6f94c178c6ffffb9",
    "email": null,
    "emailVerified": false,
    "unionid": "oiPbDuG4S7msrKHPKDc8MECSe8jM",
    "openid": "oiPbDuG4S7msrKHPKDc8MECSe8jM",
    "oauth": "{\"openid\":\"oiPbDuG4S7msrKHPKDc8MECSe8jM\",\"nickname\":\"廖长江\",\"sex\":1,\"language\":\"zh_CN\",\"city\":\"海淀\",\"province\":\"北京\",\"country\":\"中国\",\"headimgurl\":\"http://thirdwx.qlogo.cn/mmopen/vi_32/GkxYERPDdTMk7bOk3BgBmEEYul8oMcOoLgNHLoibZn5ibe4EulWBp1xo6uN4az59eoSBYBW0QmXB9TrsJEM0EoPw/132\",\"privilege\":[]}",
    "registerMethod": "oauth:wxmp",
    "username": "廖长江",
    "nickname": "廖长江",
    "company": "",
    "photo": "https://usercontents.authing.cn/avatar-5dc10bcb6f94c178c6ffffb9-1572932555337",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7InVuaW9uaWQiOiJvaVBiRHVHNFM3bXNyS0hQS0RjOE1FQ1NlOGpNIiwiaWQiOiI1ZGMxMGJjYjZmOTRjMTc4YzZmZmZmYjkiLCJjbGllbnRJZCI6IjVkYTdlZGFiNTAzOTZjMWFkOTYyMzc4YSJ9LCJpYXQiOjE1NzI5NTY0MjUsImV4cCI6MTU3NDI1MjQyNX0.OTgl72WZS8So3R5DbWCJ7I_Bd0LaZa4S0TAVMg9qaYQ",
    "tokenExpiredAt": "11/20/2019, 8:20:25 PM",
    "loginsCount": 43,
    "lastLogin": "11/5/2019, 8:20:25 PM",
    "lastIP": "127.0.0.1",
    "signedUp": "11/5/2019, 1:42:35 PM",
    "blocked": false,
    "isDeleted": false
}
```

#### 如何携带 token

将 `Authorization` 请求头设置为 "Bearer " + token，例如：

> 注意 Bearer 和 token 之间的空格

```text
Authorization: "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7InVuaW9uaWQiOiJvaVBiRHVHNFM3bXNyS0hQS0RjOE1FQ1NlOGpNIiwiaWQiOiI1ZGMxMGJjYjZmOTRjMTc4YzZmZmZmYjkiLCJjbGllbnRJZCI6IjVkYTdlZGFiNTAzOTZjMWFkOTYyMzc4YSJ9LCJpYXQiOjE1NzI5NTY0MjUsImV4cCI6MTU3NDI1MjQyNX0.OTgl72WZS8So3R5DbWCJ7I_Bd0LaZa4S0TAVMg9qaYQ"
```

如果你使用的是 axios，可以这样写：

```javascript
axios.get('https://mywebsite.com/endpoint/', {
  headers: {
    Authorization: `Bearer ${userInfo.token}`
  }
})
```

如果你使用的是 fetch，可以这样写：

```javascript
fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    Authorization: `Bearer ${userInfo.token}`
  },
  body: JSON.stringify({
    firstParam: 'yourValue',
    secondParam: 'yourOtherValue',
  }),
});
```

#### 如何在后端检验 Token

Authing 提供了几种方法帮助检验 token 的合法性和对应用户的登录状态，[文档点这里](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。

开发者可以把这个方法封装成一个函数，比如说 check\_authing\_token\_status（为了方便我使用了 Python ）：

> 开发者不用在后端存储该 token，只需要调用 Authing 提供的接口。

```python
def check_authing_token_status(token: str) -> bool:
    """
    :param token: Authing 返回用户信息中携带的 token
    :return: 布尔值，表示是否处于登录状态
    """
    # 调用 Authing 提供的方法，具体实现方法省略，请参见上文提到的文档
    pass
```

然后就可以通过登录状态和自己的业务逻辑对请求进行响应了，比如：

```python
logged_in = check_authing_token_status(token)
if not logged_in:
    # 返回错误提示
# 正常继续下面的逻辑
```

#### 如何通过用户角色控制用户访问

有时候是否登录这一个条件是不足以判断请求方是否有访问资源的权限的，为此 Authing 还提供了用户角色相关的 API。详情请查看[用户角色文档](https://learn.authing.cn/authing/advanced/roles)。

### 支持的回调函数列表

| 回调函数 | 对应事件 | 参数 | 参数说明 |
| :--- | :--- | :--- | :--- |
| onLogin | 成功登录 | userInfo | 用户信息。 |
| onLoginError | 登录失败 | `error` | 错误信息。 |
| onSocialLoad | 社会化登录列表加载完成 | oauthList | 完整的 OAuth 列表，若用户未在后台配置过则为空。注：Native 端只会显示 Native 应用的社会化登录。 |
| onSocialUnload | 社会化登录列表加载失败 | `error` | 错误信息 |
| onRegister | 用户注册成功 | userInfo | 用户数据。和 onLogin 回调函数的 `userInfo` 参数一致，但是 `token` 为空。 |
| onRegisterError | 用户注册失败 | `error` | 错误信息. |
| onEmailSent | 忘记密码邮件发送成功 | data | 发送的结果 |
| onEmailSentError | 忘记密码邮件发送失败 | `error` | 错误信息 |
| onResetPassword | 重置密码成功 | data | 重置密码结果 |
| onResetPasswordError | 重置密码失败 | `error` | 错误信息 |

### 自定义 UI

Guard 支持高度自定义，可以通过 options 参数传入，如：

```javascript
<Guard
  userPoolId={userPoolId}
  options={{
    title: '你的应用名称',
    logo: '你的应用图标',
    forceLogin: true, // 将注册和登录合并，如果用户不存在会自动创建并登录。
    placeholder: {
        username: 'xxxxx', // 自定义用户名输入框的 placeholder
    }
  }}
  onLogin={onLogin}
/>
```

#### 加入自定义 CSS

authing-rn-sdk 还支持通过 `options.css` 传入自定义 CSS 样式，这使得开发者可以高度自定义表单样式。如果指定了 `options.css`，会在 DOM 的 head 中插入一个 `<style type="text/css"></style>` 节点。 示例：

```javascript
const css = `
body {
    background: #6699 !important;
} 
`
<Guard
  userPoolId={userPoolId}
  options={{
    css,
  }}
  onLogin={onLogin}
/>
```

效果如图所示：

![authing-rn-sdk &#x793A;&#x4F8B;](http://lcjim-img.oss-cn-beijing.aliyuncs.com/2019-12-06-100834.png)

其他完整的参数请参考：[Guard 官方文档](https://github.com/Authing/Guard#ui-%E5%AE%9A%E5%88%B6)。

