---
description: 使用原生 GraphQL 与 Authing 服务器交互。
---

# Open Graphql

本文档包含了 Authing 的 GraphQL 请求，可直接复制粘贴使用。

## GraphQL 网关

1. https://users.authing.cn/graphql
2. https://oauth.authing.cn/graphql

如非特殊说明，以下 GraphQL 都使用 [https://users.authing.cn/graphql](https://users.authing.cn/graphql) 网关。

## getClientWhenSdkInit

SDK 初始化时获取客户端信息和 accessToken 信息，后续的管理用户的操作请发送此 Token，在下文中，此 Token 命名为「`OwnerToken`」。

```graphql
query getClientWhenSdkInit($secret: String, $clientId: String, $retUserId: Boolean, $timestamp: String, $signature: String, $nonce: Int){
    getClientWhenSdkInit(secret: $secret, clientId: $clientId, retUserId: $retUserId, timestamp: $timestamp, signature: $signature, nonce: $nonce){
      accessToken
      clientInfo {
        _id
        name
        descriptions
        jwtExpired
        createdAt
        isDeleted
        logo
        emailVerifiedDefault
        registerDisabled
        allowedOrigins
        clientType {
          _id
          name
          description
          image
          example
        }
      }
    }
}
```

### **注意事项**

此接口不需要发送任何 `Token`

## ReadOAuthList

此接口用来读取用户在 Authing 控制台中配置的 OAuth 信息，enabled 为是否启用。

{% hint style="info" %}
`此接口使用的网关为：https://oauth.authing.cn/graphql`
{% endhint %}

```graphql
query ReadOAuthList($clientId: String!) {
    ReadOauthList(clientId: $clientId) {
        _id
        name
        image
        description
        enabled
        client
        user
        url
    }
}
```

### **注意事项**

此接口不需要发送任何 `Token`

## login

此接口用来执行用户登录的操作，登录成功后会返回 `UserToken`，建议单独维护此 Token。

```graphql
mutation login($unionid: String, $email: String, $password: String, $lastIP: String, $registerInClient: String!, $verifyCode: String, $browser: String, $device: String) {
    login(unionid: $unionid, email: $email, password: $password, lastIP: $lastIP, registerInClient: $registerInClient, verifyCode: $verifyCode, browser: $browser, device: $device) {
        _id
        email
        emailVerified
        username
        nickname
        company
        photo
        browser
        token
        tokenExpiredAt
        loginsCount
        lastLogin
        lastIP
        signedUp
        blocked
        isDeleted
    }
}
```

### **注意事项**

1. 此处（登录）的密码需要加密，Authing 使用了非对称加密算法，加密方式是 `PKCS1v1.5`。注意，个别语言，如 JavaScript（非 Node ）和 Go 在加密之后还要使用 `base64` 包一层；
2. 此接口不需要发送任何 `Token`。

### **Public Key**

```text
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC4xKeUgQ+Aoz7TLfAfs9+paePb
5KIofVthEopwrXFkp8OCeocaTHt9ICjTT2QeJh6cZaDaArfZ873GPUn00eOIZ7Ae
+TiA2BKHbCvloW3w5Lnqm70iSsUi5Fmu9/2+68GZRH9L7Mlh8cFksCicW2Y2W2uM
GKl64GDcIq3au+aqJQIDAQAB
-----END PUBLIC KEY-----
```

## loginByPhoneCode

使用手机验证码的方式登录，登录后返回的 Token 需要在客户端维护。

```graphql
mutation login($phone: String, $phoneCode: Int, $registerInClient: String!, $browser: String, $device: String) {
          login(phone: $phone, phoneCode: $phoneCode, registerInClient: $registerInClient, browser: $browser, device: $device) {
            _id
            email
            emailVerified
            username
            nickname
            phone
            company
            photo
            browser
            token
            tokenExpiredAt
            loginsCount
            lastLogin
            lastIP
            signedUp
            blocked
            isDeleted
          }
      }
```

### **注意事项**

此接口不用发送任何 `Token`。

### 发送短信验证码

发送短信验证码是一个 rest 接口，接口声明如下：

{% api-method method="get" host="https://users.authing.cn" path="/send\_smscode/:phone/:clientId" %}
{% api-method-summary %}
 发送短信验证码
{% endapi-method-summary %}

{% api-method-description %}
 发送验证码给指定手机号
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="clientId" type="string" required=true %}
 应用 ID
{% endapi-method-parameter %}

{% api-method-parameter name="phone" type="string" required=true %}
  手机号
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{"code":200,"message":"发送成功"}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## loginByLDAP

 使用 LDAP 登录，登录后返回的 Token 需要在客户端维护

```graphql
mutation LoginByLDAP($username: String!, $password: String!, $clientId: String!, $browser: String, $device: String) {
      LoginByLDAP(username: $username, clientId: $clientId, password: $password, browser: $browser, device: $device) {
            _id
            email
            emailVerified
            unionid
            oauth
            registerMethod
            username
            nickname
            company
            photo
            browser
            token
            tokenExpiredAt
            loginsCount
            lastLogin
            lastIP
            signedUp
            blocked
          }
      }
```

### **注意事项**

此接口不用发送任何 `Token`。

## refreshToken

刷新用户 Token

```graphql
mutation RefreshToken(
  $client: String!
  $user: String!
) {
  refreshToken(
    client: $client
    user: $user
  ) {
    token
    iat
    exp
  }
}
```

### **注意事项**

此接口需发送 `OwnerToken`。

## register

```graphql
mutation register(
    $unionid: String,
    $email: String, 
    $password: String, 
    $lastIP: String, 
    $forceLogin: Boolean,
    $registerInClient: String!,
    $oauth: String,
    $username: String,
    $nickname: String,
    $registerMethod: String,
    $photo: String,
    $browser: String, 
    $device: String
) {
    register(userInfo: {
        unionid: $unionid,
        email: $email,
        password: $password,
        lastIP: $lastIP,
        forceLogin: $forceLogin,
        registerInClient: $registerInClient,
        oauth: $oauth,
        registerMethod: $registerMethod,
        photo: $photo,
        username: $username,
        nickname: $nickname,
        device: $device,
        browser: $browser
    }) {
        _id,
        email,
        emailVerified,
        username,
        nickname,
        company,
        photo,
        browser,
        password,
        token,
        group {
            name
        },
        blocked,
        device
    }
}
```

### **注意事项**

1. 此处（登录）的密码需要加密，Authing 使用了非对称加密算法，加密方式是 `PKCS1v1.5`。注意，个别语言，如 JavaScript（非 Node）和 Go 在加密之后还要使用 `base64` 包一层。
2. 此接口不需要发送任何 `Token`。

### **Public Key**

```text
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC4xKeUgQ+Aoz7TLfAfs9+paePb
5KIofVthEopwrXFkp8OCeocaTHt9ICjTT2QeJh6cZaDaArfZ873GPUn00eOIZ7Ae
+TiA2BKHbCvloW3w5Lnqm70iSsUi5Fmu9/2+68GZRH9L7Mlh8cFksCicW2Y2W2uM
GKl64GDcIq3au+aqJQIDAQAB
-----END PUBLIC KEY-----
```

## user

此接口用来读取用户资料，建议使用 `OwnerToken`

```graphql
query user($id: String!, $registerInClient: String!){
    user(id: $id, registerInClient: $registerInClient) {
        _id
        email
        emailVerified
        username
        nickname
        company
        photo
        browser
        registerInClient
        registerMethod
        oauth
        token
        tokenExpiredAt
        loginsCount
        lastLogin
        lastIP
        signedUp
        blocked
        isDeleted
        userLocation {
          _id
          when
          where
        }
        userLoginHistory {
          totalCount
          list {
            _id
            when
            success
            ip
            result
          }
        }
    }
}
```

### **注意事项**

此接口需要发送 Token，建议直接使用 `OwnerToken`。

## userPatch

批量查询用户

```graphql
query userPatch($ids: String){
    userPatch(ids: $ids){
      list {
        _id
        unionid
        email
        emailVerified
        username
        nickname
        company
        photo
        browser
        registerInClient
        registerMethod
        oauth
        token
        tokenExpiredAt
        loginsCount
        lastLogin
        lastIP
        signedUp
        blocked
        isDeleted
        userLocation {
          _id
          when
          where
        }
        userLoginHistory {
          totalCount
          list {
            _id
            when
            success
            ip
            result
          }
        }
      }
      totalCount
    }
}
```

### **注意事项**

此接口不用发送任何 `Token`。

## list

此接口用来读取用户列表，建议使用 `OwnerToken`

```graphql
query users($registerInClient: String, $page: Int, $count: Int){
  users(registerInClient: $registerInClient, page: $page, count: $count) {
    totalCount
    list {
      _id
      email
      emailVerified
      username
      nickname
      company
      photo
      browser
      password
      registerInClient
      token
      tokenExpiredAt
      loginsCount
      lastLogin
      lastIP
      signedUp
      blocked
      isDeleted
      group {
        _id
        name
        descriptions
        createdAt
      }
      clientType {
        _id
        name
        description
        image
        example
      }
      userLocation {
        _id
        when
        where
      }
      userLoginHistory {
        totalCount
        list{
          _id
          when
          success
          ip
          result
        }
      }
      systemApplicationType {
        _id
        name
        descriptions
        price
      }
    }
  }
}
```

### **注意事项**

此接口需要发送 Token，建议直接使用 `OwnerToken`。

## checkLoginStatus

此接口检查用户登录状态，请使用 `UserToken`

```graphql
query checkLoginStatus {
    checkLoginStatus {
        status
        code
        message
    }
}
```

### **注意事项**

此接口需要发送 Token，请使用 `UserToken`。

## remove

此接口用来删除用户数据，建议使用 `OwnerToken`

```text
mutation removeUsers($ids: [String], $registerInClient: String, $operator: String){
  removeUsers(ids: $ids, registerInClient: $registerInClient, operator: $operator) {
    _id
  }
}
```

### **注意事项**

此接口需要发送 Token，建议直接使用 `OwnerToken`。

## update

此接口用来更新用户资料，建议使用 `OwnerToken`

```graphql
mutation UpdateUser(
    $_id: String!,
    $email: String,
    $emailVerified: Boolean,
    $username: String,
    $nickname: String,
    $company: String,
    $photo: String,
    $browser: String,
    $password: String,
    $oldPassword: String,
    $registerInClient: String!,
    $token: String,
    $tokenExpiredAt: String,
    $loginsCount: Int,
    $lastLogin: String,
    $lastIP: String,
    $signedUp: String,
    $blocked: Boolean,
    $isDeleted: Boolean
){
  updateUser(options: {
    _id: $_id,
    email: $email,
    emailVerified: $emailVerified,
    username: $username,
    nickname: $nickname,
    company: $company,
    photo: $photo,
    browser: $browser,
    password: $password,
    oldPassword: $oldPassword,
    registerInClient: $registerInClient,
    token: $token,
    tokenExpiredAt: $tokenExpiredAt,
    loginsCount: $loginsCount,
    lastLogin: $lastLogin,
    lastIP: $lastIP,
    signedUp: $signedUp,
    blocked: $blocked,
    isDeleted: $isDeleted
  }) {
    _id
    email
    emailVerified
    username
    nickname
    company
    photo
    browser
    registerInClient
    registerMethod
    oauth
    token
    tokenExpiredAt
    loginsCount
    lastLogin
    lastIP
    signedUp
    blocked
    isDeleted
  }
}
```

### **注意事项**

此接口需要发送 Token，建议直接使用 `OwnerToken`。

## 重置密码流程

1. sendResetPasswordEmail 发送重置密码邮件给用户 
2. verifyResetPasswordVerifyCode 检查验证码是否正确
3. changePassword 使用新的密码和验证码来修改密码

### **注意事项**

以下三个修改密码的 Token 可以不发送任何`Token`。

### sendResetPasswordEmail

```graphql
mutation sendResetPasswordEmail(
    $email: String!,
    $client: String!
){
    sendResetPasswordEmail(
        email: $email,
        client: $client
    ) {
          message
          code
          status
    }
}
```

### verifyResetPasswordVerifyCode

```graphql
mutation verifyResetPasswordVerifyCode(
    $email: String!,
    $client: String!,
    $verifyCode: String!
) {
    verifyResetPasswordVerifyCode(
        email: $email,
        client: $client,
        verifyCode: $verifyCode
    ) {
          message
          code
          status
    }
}
```

### changePassword

此接口用来更改忘记密码后的新密码，需要携带 verifyCode，不用发送 `Token`，正常的密码修正请使用上面的 `update` 接口。

```graphql
mutation changePassword(
    $email: String!,
    $client: String!,
    $password: String!,
    $verifyCode: String!
){
    changePassword(
        email: $email,
        client: $client,
        password: $password,
        verifyCode: $verifyCode
    ) {
        _id
        email
        emailVerified
        username
        nickname
        company
        photo
        browser
        registerInClient
        registerMethod
        oauth
        token
        tokenExpiredAt
        loginsCount
        lastLogin
        lastIP
        signedUp
        blocked
        isDeleted
    }
}
```

## sendVerifyEmail

```graphql
mutation sendVerifyEmail(
    $email: String!,
    $client: String!
){
    sendVerifyEmail(
        email: $email,
        client: $client
    ) {
        message,
        code,
        status
    }
}
```

### **注意事项**

此接口不用发送任何 `Token`。

## decodeJwtToken

解析 JWT Token

```graphql
query decodeJwtToken($token: String){
    decodeJwtToken(token: $token){
        data{
            email
            id
            clientId
            unionid
        }
        status{
            message
            code
            status
        }
        iat
        exp
    }
}
```

### **注意事项**

此接口不用发送任何 `Token`。

## bindOtherOAuth

用户绑定第三方登录方式

```graphql
mutation bindOtherOAuth($user: String, $client: String, $type: String!, $unionid: String!, $userInfo: String!){
    bindOtherOAuth(user: $user, client: $client, type: $type, unionid: $unionid, userInfo: $userInfo){
        _id
        user
        client
        type
        unionid
        userInfo
        createdAt
    }
}
```

### **注意事项**

此接口发送 `UserToken`。

## unbindOtherOAuth

用户解绑第三方登录方式

```graphql
mutation unbindOtherOAuth($user: String, $client: String, $type: String!){
    unbindOtherOAuth(user: $user, client: $client, type: $type){
        _id
        user
        client
        type
        unionid
        userInfo
        createdAt
    }
}
```

### **注意事项**

此接口发送 `UserToken`。

## unbindEmail

用户解绑 Email

```graphql
mutation unbindEmail($user: String, $client: String){
    unbindEmail(user: $user, client: $client){
      _id
      email
      emailVerified
      username
      nickname
      company
      photo
      browser
      registerInClient
      registerMethod
      oauth
      token
      tokenExpiredAt
      loginsCount
      lastLogin
      lastIP
      signedUp
      blocked
      isDeleted
    }
}
```

### **注意事项**

此接口发送 `UserToken`。

