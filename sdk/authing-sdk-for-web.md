# SDK for Web

JavaScript SDK 支持 **Angular.js**, **React.js**, **Vue.js** 以及 **Node.js**.我们提供了完全一致的接口。

## 安装

### **NPM**

当构建大规模应用时，我们推荐使用 `npm` 进行安装， 它可以与一些模块打包工具很好地配合使用，如 `Webpack`， `Browserify。`

```bash
# latest stable
$ npm install authing-js-sdk --save
```

## 初始化

先从 [Authing 控制台](https://authing.cn/dashboard) 中 [获取 Client ID](https://docs.authing.cn/#/quick_start/howto)。

为保证 secret 安全，在服务端的初始化和客户端的初始化有所不同。

#### 服务端

服务端可直接传入 `clientId` 和 `secret`。

```javascript
const auth = new Authing({
	clientId: 'your_client_id',
	secret: 'your_client_secret'
});

auth.then((authing) => {
	// authing.login
	// authing.register
	// ...
});
```

#### 客户端

**客户端需传入三个参数**

* **clientId**
  * 应用 ID，可从 [Authing 控制台](https://authing.cn/dashboard)中[获取](https://docs.authing.cn/#/quick_start/howto)。
* **timestamp**
  * 当前时间戳 `Math.round(new Date() / 1000)`
* **nonce**
  * 一个随机数字，不要超过十位数

**示例**

```javascript
const auth = new Authing({
	clientId: 'your_client_id',
	timestamp: Math.round(new Date() / 1000),
	nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
});
auth.then((authing) => {
	// authing.login
	// authing.register
	// ...
});
```

## 使用

Authing SDK 的所有 API 都支持 **Promise**。

```javascript
const Authing = require('authing-js-sdk');

// 对 Client ID 和 Client Secret 进行验证，获取 Access Token
const auth = new Authing({
	// 若在浏览器端请使用 timestamp + nonce + clientId 的形式	
	clientId: 'your_client_id',
	secret: 'your_app_secret' 
});

auth.then(function(authing) {

	//验证成功后返回新的 authing-js-sdk 实例(authing)，可以将此实例挂在全局

	authing.login({
		email: 'test@testmail.com',
		password: 'testpassword'
	}).then(function(user) {
		console.log(user);	
	}).catch(function(error) {
		console.log(error);	
	});
	
}).catch(function(error) {
	//验证失败
	console.log(error);
});
```

如果你使用 `ES6+` 推荐用 `await` 处理异步，示例如下：

```javascript
import Authing from 'authing-js-sdk';

const main = async () => {

	//使用async时需要使用 try...catch... 捕捉错误

	let auth;

	try{
		auth = await new Authing({
			// 若在浏览器端请使用 timestamp + nonce + clientId 的形式			
			clientId: 'your_client_id',
			secret: 'your_app_secret'
		});
	}catch(error) {
		console.log('Authing 验证失败:', error);
	}

	if(auth) {

		let user;

		try {
			user = await auth.login({
				email: 'test@testmail.com',
				password: 'testpassword'
			});
		}catch(error) {
			console.log('登录失败:', error);
		}

		if(user) {
			console.log('login success');
		}else {
			console.log('login failed');
		}

	}

}

main();
```

## 注册

**Authing.register\(options\)**

* **参数:**
  * `{Object} options`
    * **email**
    * **password**
    * **unionid**，若不使用 email 和 password 则 unionid 必选
    * **oauth** 可选，oauth 信息的字符串，或者其他自定义的用户字段都可以以 JSON 字符串的形式存在这里
    * **username**，可选，用户名
    * **nickname**，可选，昵称
    * **company**，可选，公司名称
    * **photo**，可选，用户头像
    * **lastIP**，可选，用户登录的 IP 地址
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const userInfo = await authing.register({
        email: email,
        password: password
      }).catch((error) => { ... });;
    })();
    ```
* **返回数据:**
  * ```javascript
    {
        "_id": "59e5ff4935eebf1913cfe8a1",
        "email": "test@test.com",
        "emailVerified": false,
        "username": "test@test.com",
        "nickname": "",
        "company": "",
        "photo": "http://www.xiaohehe.net/uploads/allimg/150305/304-1503051H136.png",
        "browser": "",
        "token": null,
        "tokenExpiredAt": null,
        "loginsCount": 0,
        "lastLogin": "Tue Oct 17 2017 21:02:01 GMT+0800 (CST)",
        "lastIP": null,
        "signedUp": "Tue Oct 17 2017 21:02:01 GMT+0800 (CST)",
        "blocked": false,
        "isDeleted": false,
        "group": {
          "_id": "59e374332023830871913ebd",
          "name": "default",
          "descriptions": "default",
          "createdAt": "Sun Oct 15 2017 22:44:03 GMT+0800 (CST)"
        }
    }
    ```

## 登录

**Authing.login\(options\)**

* **参数:**
  * `{Object} options`
    * **email**
    * **password**
    * **unionid，**若不使用 email 和 password 则 unioinid 必选
    * **verifyCode，**可选，频繁注册时会要求输入验证码，返回数据会包含
    * **lastIP，**可选，若连续出现验证码验证失败情况，请将客户端 IP 填入
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const userInfo = await authing.register({
        email: email,
        password: password
      }).catch((error) => {
        /* 如果错误信息提示需要输入验证码，则登录参数应为
          { 
            email: email, 
            password: password, 
             verifyCode: verifyCode 
           } 
         /* 
      });
    })();
    ```
* **返回数据:**
  * ```javascript
    {
        "_id": "59e5ff4935eebf1913cfe8a1",
        "email": "86700229ww6ss@163.com",
        "emailVerified": false,
        "username": "86700229ww6ss@163.com",
        "nickname": "",
        "company": "",
        "photo": "http://www.xiaohehe.net/uploads/allimg/150305/304-1503051H136.png",
        "browser": "",
        "token": null,
        "tokenExpiredAt": null,
        "loginsCount": 0,
        "lastLogin": "Tue Oct 17 2017 21:02:01 GMT+0800 (CST)",
        "lastIP": null,
        "signedUp": "Tue Oct 17 2017 21:02:01 GMT+0800 (CST)",
        "blocked": false,
        "isDeleted": false,
        "group": {
          "_id": "59e374332023830871913ebd",
          "name": "default",
          "descriptions": "default",
          "createdAt": "Sun Oct 15 2017 22:44:03 GMT+0800 (CST)"
        }
     }
    ```

## 使用手机验证码登录

**Authing.loginByPhoneCode\(options\)**

* **参数:**
  * `{Object} options`
    * **phone**，必填，手机号
    * **phoneCode**，必填，使用[发送短信接口](https://github.com/Authing/docs/blob/master/user_service/send_phone_code.md)获取
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const userInfo = await authing.loginByPhoneCode({
        phone: 'Your Phone',
        phoneCode: 'Your Phone Code',
      }).catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
    {
        "_id": "59e5ff4935eebf1913cfe8a1",
        "email": "用户邮箱",
        "emailVerified": false,
        "username": "用户名",
        "nickname": "",
        "phone": "用户手机号",
        "company": "",
        "photo": "https://usercontents.authing.cn/client/logo@2.png",
        "browser": "",
        "token": null,
        "tokenExpiredAt": null,
        "loginsCount": 0,
        "lastLogin": "Tue Oct 17 2017 21:02:01 GMT+0800 (CST)",
        "lastIP": null,
        "signedUp": "Tue Oct 17 2017 21:02:01 GMT+0800 (CST)",
        "blocked": false,
        "isDeleted": false,
    }
    ```

## 使用 LDAP 登录

LDAP 服务的配置流程请参考[配置 LDAP 服务](https://learn.authing.cn/authing/advanced/sso/ldap)。

**Authing.loginByLDAP\(options\)**

* **参数:**
  * `{Object} options`
    * `username`，在 LDAP 服务中的登录名，可以是邮箱或用户名
    * `password`，在 LDAP 服务中的密码
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const userInfo = await authing.loginByLDAP({
        username: 'Your LDAP username',
        passowrd: 'Your LDAP password',
      }).catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "_id": "5cbe9a4347618069372e75ff",
      "username": "Nikola Tesla",
      "nickname": "Nikola Tesla",
      "oauth": "{\"dn\":\"uid=tesla,dc=example,dc=com\",\"controls\":[],\"objectClass\":[\"inetOrgPerson\",\"organizationalPerson\",\"person\",\"top\",\"posixAccount\"],\"cn\":\"Nikola Tesla\",\"sn\":\"Tesla\",\"uid\":\"tesla\",\"mail\":\"tesla@ldap.forumsys.com\",\"uidNumber\":\"88888\",\"gidNumber\":\"99999\",\"homeDirectory\":\"home\"}",
      "unionid": "uid=tesla,dc=example,dc=com",
      "registerMethod": "ldap:default::from-undefined",
      "email": "tesla@ldap.forumsys.com",
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoidGVzbGFAbGRhcC5mb3J1bXN5cy5jb20iLCJ1bmlvbmlkIjoidWlkPXRlc2xhLGRjPWV4YW1wbGUsZGM9Y29tIiwiaWQiOiI1Y2JlOWE0MzQ3NjE4MDY5MzcyZTc1ZmYiLCJjbGllbnRJZCI6IjVjYTQwMjdjOGEzNjJjODIyZTI2ZDA0ZiJ9LCJpYXQiOjE1NTU5OTY1MjUsImV4cCI6MTU1NzI5MjUyNX0.gcKasWAzO0ZS4ay_KGGp_ihPKG_GaGXk5iQKzP6R4_w",
      "company": ""
    }
    ```

## 退出登录

**Authing.logout\(uid\)**

* **参数:**
  * `{String} uid`，必填，用户的 \_id
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      await authing.logout('59e5ff4935eebf1913cfe8a1')
        .catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
    {
    	id: "59e5ff4935eebf1913cfe8a1"
    }
    ```

### 退出 SSO

如果你使用了 OAuth、OIDC 或 SAML 实现了单点登录，那么使用户退出登录需要跳转到一个 URL：

> https://&lt;你的域名&gt;.authing.cn/login/profile/logout?app\_id=&lt;OAuth 应用 ID&gt;&redirect\_uri=&lt;退出之后的回调地址&gt;

其中 `app_id` 和 `redirect_uri` 都是必填选项，`redirect_uri` 是退出后你想要返回的地址。

## 验证 JWT Token 的合法性以及是否过期

验证 JWT 的合法性需要使用应用的密钥，密钥在控制台中可以获取到。

以下以 Node 为例（需要安装 `jsonwebtoken`）。

```javascript
const jwt = require('jsonwebtoken');

try {
  let decoded = jwt.verify('JSON Web Token from client', 'your_secret'),
    expired = (Date.parse(new Date()) / 1000) > decoded.exp
  if (expired) {
    // 过期
  }else {
    // 合法也没过期，正常放行
  }
} catch (error) {
  // 不合法
}
```

## 小程序扫码登录

小程序扫码登录指使用 Authing 小程序 `身份管家` 执行微信登录。 示例：[小程序扫码登录](http://sample.authing.cn/)

[![&#x626B;&#x7801; demo](https://camo.githubusercontent.com/1cc34581f6a1b0749f820282d685734f55a57993/68747470733a2f2f75736572636f6e74656e74732e61757468696e672e636e2f77786170702d7363616e696e672d64656d6f2e676966)](https://camo.githubusercontent.com/1cc34581f6a1b0749f820282d685734f55a57993/68747470733a2f2f75736572636f6e74656e74732e61757468696e672e636e2f77786170702d7363616e696e672d64656d6f2e676966)

####  使用方法请参考：

{% page-ref page="../advanced/wxapp-qrcode.md" %}

## 自定义请求链接

`Authing` 构造函数包含一个名为 `host` 对象，可接收自定义的请求链接（适合私有部署 Authing 的用户使用），使用方法如下：

```javascript
const auth = new Authing({
	clientId: 'xxxx',
	secret: 'xxxxxx',
	host: {
		user: 'https://users.authing.cn/graphql',
		oauth: 'https://oauth.authing.cn/graphql'
	}
});
```

`host` 对象中 `user` 和 `oauth` 的默认值是 Authing 官方的请求链接：

* [https://users.authing.cn/graphql](https://users.authing.cn/graphql)
* [https://oauth.authing.cn/graphql](https://oauth.authing.cn/graphql)

  


