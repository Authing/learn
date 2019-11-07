---
description: Webhooks 允许你对用户注册、登录等行为进行监听，从而对其做一些自定义处理。
---

# 使用 Webhook

使用 Webhook 的方法是在 Authing 平台中配置 HTTP URL，当你的用户登录、注册、修改密码后，都会给远程 HTTP URL 发送一个 POST 请求。

## 配置 Webhook

![](../.gitbook/assets/image%20%28228%29.png)

点击页面的「添加」按钮即可开始配置，如下图所示：

![](../.gitbook/assets/image%20%2844%29.png)

### 参数解释

| 参数名称 | 参数解释 |
| :--- | :--- |
| 请求地址 | 远程 HTTP URL 地址 |
| 请求密钥 | 设置好密钥（值由开发者自定）后，Authing 将在每个请求中（HTTP Header：X-Authing-Token）附带此密钥，你可以通过验证此密钥来避免一些非法操作 |
| 请求数据格式 | 指定发起 Webhook 请求时 Request body 的数据格式，可选值有 `application/json` 和 `application/x-www-form-urlencoded` |
| 触发事件 | [请查看支持的事件](http://docs.authing.cn/authing/advanced/use-webhook#zhi-chi-de-shi-jian) |
| 激活 | 是否启用此 Hook |

## 管理 Webhook

在创建完 Webhook 之后可以看到详细的事件记录：

![](../.gitbook/assets/image%20%282%29.png)

刚创建好的 Hook 请求事件都为空，这时你可以点击「测试」触发一个「测试事件」：

![](../.gitbook/assets/image%20%28229%29.png)

测试成功后你将看到详细的请求信息和返回信息。

## 调试 Webhook

 调试 Webhook 的方法如下图所示：

![](../.gitbook/assets/image%20%28183%29.png)

点击后将发送一个 Post 请求到配置好的 HTTP URL 中。

请求数据为：

```javascript
{    "description": "A test from Authing Webhook"}
```

## 支持的事件

### 事件列表

| 事件名 | 事件描述 |
| :--- | :--- |
| login | 登录事件，当用户进行登录时会触发此事件，无论是否登录成功都会触发。 |
| register | 注册事件，当用户注册或管理员手动创建用户时会触发此事件，无论是否成功都会触发 |
| change-password | 修改密码事件，当用户修改密码或管理员手动修改密码时会触发此事件，无论成功与否都会触发 |
| change-user-info | 修改用户信息时间，当用户修改自己的信息或管理员手动修改用户信息时会触发词时间，无论成功与否都会触发 |

### 请求类型

指定发起 Webhook 请求时 Request body 的数据格式，可选值有 `application/json` 和 `application/x-www-form-urlencoded`

## 附带的数据

每一个事件都会携带一些特定的请求参数。

### Request headers

我们会在 HTTP POST 头中携带一些自定义头信息，如下表所示：

| Header | 描述 |
| :--- | :--- |
| `User-Agent` | 值为 `'Authing-hook'`，表示此次请求来自 Authing |
| `X-Authing-Token` | 请求秘钥，值为你在 Webhook 配置中设置的秘钥，对此秘钥进行验证，可以用来防止第三方的恶意请求 |
| `X-Authing-Event` | 本次 hook 触发的事件，值为上述 `Events` 列表中的一个 |

### Request body

请求体中也会携带一些特定参数

| 参数名 | 描述 |
| :--- | :--- |
| `success` | 本次事件是否成功，0 为失败，1 为成功 |
| `message` | 本次事件的结果描述 |
| `executed_at` | 本次事件执行时的时间戳，单位为 `ms` |
| `params` | 用户进行此次操作时携带的参数 |
| `emit_by` | `change-password、change-user-info` 和 `register` 事件特有，表示本次事件的发起者，值为用户自己或用户管理员的对象数据 |
| user\_updated | `change-user-info` 事件特有，内容为用户修改后的用户信息 |

#### Request 示例

* headers

  ```javascript
  {  "Accept": "application/json, text/plain, */*",  "Content-Type": "application/json; charset=UTF-8",  "User-Agent": "authing-hook",  "X-Authing-Token": "",  "X-Authing-Event": "login",  "Content-Length": 337}
  ```

* login event body

  ```javascript
  {  "success": 1,  "message": "密码修改成功",  "executed_at": 1559453952531,  "params": {      "_id": "5cf3608753c403913e81f74b",      "password": "ATtLOFJ6mGWCju1GFsv6b0jhlFTlp7qumPYSEze197R7G0hUEg3gDVBwRf2n0O6FJFeF9bsVryzkZDElILVSRAbjYU3GPYNDzUMAfo3pQ7QNKu5JuZOdwgn8+5NllFUKNK/AgyJPd7QNfO7kH1x8J9L7S65NQh/n5TzgcwaveLg=",      "registerInClient": "59f86b4832eb28071bdd9214"  },  "emit_by": {      "_id": "5c00a5fbec1083000f5b27d4",      "username": "",      "email": "xieyang@dodora.cn",      "phone": ""  }}
  ```

* register event body

  ```javascript
  {  "success": 1,  "message": "注册成功",  "executedAt": 1559453155297,  "params": {      "_id": "590cd6b4832eb28071bdd9251"      "email": "example@example.com",      "password": "30f049f7ae9386d2ac2c203f5c4319a5",      "registerInClient": "59f86b4832eb28071bdd9214",      "username": "username",      "registerMethod": "default:username-password",      "nickname": "",      "emailVerified": true  }}
  ```

* change-password event body

  ```javascript
  {  "success": 1,  "message": "注册成功",  "executedAt": 1559453831284,  "params": {      "__v": 0,      "email": "example@example2.com",      "registerInClient": "59f86b4832eb28071bdd9214",      "salt": "fhnli5d0ahoi",      "_id": "5cf3608753c403913e81f74b",      "updatedAt": "",      "country": "",      "postalCode": "",      "region": "",      "locality": "",      "streetAddress": "",      "formatted": "",      "address": "",      "locale": "",      "zoneinfo": "",      "birthdate": "",      "gender": "",      "website": "",      "preferredUsername": "",      "profile": "",      "middleName": "",      "familyName": "",      "givenName": "",      "name": "",      "phoneCode": "",      "oauth": "",      "isDeleted": false,      "blocked": false,      "signedUp": "2019-06-02T05:37:11.257Z",      "lastLogin": "2019-06-02T05:37:11.257Z",      "registerMethod": "default:username-password",      "loginsCount": 0,      "password": "bdcff42caccb7e2a889bfb490d91e67c",      "browser": "",      "photo": "https://usercontents.authing.cn/authing-avatar.png",      "company": "",      "nickname": "",      "username": "example2",      "phoneVerfified": false,      "emailVerified": true,      "phone": ""  },  "emit_by": {      "_id": "5c00a5fbec1083000f5b27d4",      "username": "root",      "email": "xieyang@dodora.cn",      "phone": ""  }}
  ```

* change-user-info event body

  ```javascript
  {  "success": 1,  "message": "用户信息修改成功",  "executed_at": 1563370136729,  "params": {    "_id": "5d2d727b48ceaceff6d792c1",    "phone": "13812341235",    "registerInClient": "5cbd6716aaaa70cb9a58d86f"  },  "emit_by": {    "_id": "5cbd66dfaaaa7020e758d86a",    "username": "",    "email": "root@example.com",    "phone": ""  },  "userUpdated": {    "_id": "5d2d727b48ceaceff6d792c1",    "email": "authing@example.com",    "registerInClient": "5cbd6716aaaa70cb9a58d86f",    "salt": "g196j8ijf1oi",    "__v": 0,    "updatedAt": "",    "country": "",    "postalCode": "",    "region": "",    "locality": "",    "streetAddress": "",    "formatted": "",    "address": "",    "locale": "",    "zoneinfo": "",    "birthdate": "",    "gender": "",    "website": "",    "preferredUsername": "",    "profile": "",    "middleName": "",    "familyName": "",    "givenName": "",    "name": "",    "phoneCode": "",    "oauth": "",    "isDeleted": false,    "blocked": false,    "signedUp": "2019-07-16T06:45:15.935Z",    "lastLogin": "2019-07-16T06:45:15.935Z",    "registerMethod": "default:username-password",    "loginsCount": 0,    "password": "3f1c2988c870c7225320a2c8a38ee27d",    "device": "",    "browser": "",    "photo": "https://usercontents.authing.cn/authing-avatar.png",    "company": "",    "nickname": "",    "username": "example",    "phoneVerfified": false,    "emailVerified": false,    "phone": "13812341235"  }}
  ```

