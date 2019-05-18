# SDK for Web

JavaScript SDK 支持 **Angular.js**, **React.js**, **Vue.js** 以及 **Node.js**.我们提供了完全一致的接口。

## 安装

**NPM**

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

## 发送手机验证码

此接口可结合[使用手机验证码登录](https://github.com/Authing/docs/blob/master/user_serivce/login_by_phone_code)使用。

**Authing.getVerificationCode\(phone\)**

* **参数:**
  * `{String} phone`
  * 手机号
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const userInfo = await authing.getVerificationCode('phone number')
        .catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      code: 200, // 500 为失败
      message: '发送成功'
    }
    ```

**短信模版**

> 【Authing】{S8} 是你的验证码，有效时间为 {S2} 分钟。如非本人操作请忽略。

{% hint style="info" %}
当前不支持修改短信模版。
{% endhint %}

验证码接口可结合[使用手机验证码登录](https://learn.authing.cn/authing/sdk/authing-sdk-for-web#shi-yong-shou-ji-yan-zheng-ma-deng-lu)使用。

## 使用手机验证码登录

**Authing.loginByPhoneCode\(options\)**

* **参数:**
  * `{Object} options`
    * **phone**，必填，手机号
    * **phoneCode**，必填，使用发送短信接口获取
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

## 退出

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

## 管理用户权限

{% page-ref page="../../advanced/roles.md" %}

{% page-ref page="web-sdk-for-roles.md" %}

## 获取单个用户资料

**Authing.user\(options\)**

* **参数:**
  * `{Object} options`
    * **id**，必填，用户的 \_id
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const userInfo = await authing.user('59e5ff4935eebf1913cfe8a1')
        .catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
      {
        "_id": "5a584dcd32e6510001a8f144", 
        "email": "1968198962@qq.com", 
        "emailVerified": false, 
        "username": "1968198962@qq.com", 
        "nickname": "", 
        "company": "", 
        "photo": "http://oxacbp94f.bkt.clouddn.com/user-avatars/Fqy_de1Jj5TmngEFiiY1-RsCCDcO", 
        "browser": "", 
        "registerInClient": "59f86b4832eb28071bdd9214", 
        "registerMethod": "default:username-password", 
        "oauth": "", 
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiMTk2ODE5ODk2MkBxcS5jb20iLCJpZCI6IjVhNTg0ZGNkMzJlNjUxMDAwMWE4ZjE0NCJ9LCJpYXQiOjE1MTcwMzI1MjV9.Ah0Oii741L_wJHhiE5KtWDgRU1Q3x_fNZBNNM5MhqDc", 
        "tokenExpiredAt": "Sat Jan 27 2018 13:55:25 GMT+0800 (CST)", 
        "loginsCount": 0, 
        "lastLogin": "Fri Jan 12 2018 13:55:25 GMT+0800 (CST)", 
        "lastIP": null, 
        "signedUp": "Fri Jan 12 2018 13:55:25 GMT+0800 (CST)", 
        "blocked": false, 
        "isDeleted": false, 
        "__typename": "ExtendUser"
    }
    ```

## 一次性获取多个用户的资料 <a id="&#x4E00;&#x6B21;&#x6027;&#x83B7;&#x53D6;&#x591A;&#x4E2A;&#x7528;&#x6237;&#x7684;&#x8D44;&#x6599;"></a>

**Authing.userPatch\(options\)**

* **参数:**
  * `{Object} options`
    * ids
      * 要获取的用户 id 列表，使用逗号分割，不要出现多余的空格；如果使用非法 id 查询时系统会自动忽略。
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const userInfo = await authing.userPatch({
        ids: '5a584dcd32e6510001a8f144,5c08fa74583d9d00019d245e'
      })
        .catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
    { list: 
    [ { _id: '5c0a3565583d9d000219d3960',
       unionid: 'oVzVG4wo1v0c7uyiFQtHc3Nrl2hg',
       email: null,
       emailVerified: false,
       username: 'xxxxxxxxxxxxxxxxx',
       nickname: 'xxxxxxxxxxxxxxxxx',
       company: '',
       photo: 'xxxxxxxxxxxxxxxxx',
       browser: '',
       registerInClient: 'xxxxxxxxxxxxxxxxx',
       registerMethod: 'oauth:wechat',
       oauth: 'xxxxxxxxxxxxxxxxx',
       token: 'xxxxxxxxxxxxxxxxx',
       tokenExpiredAt: 'Sat Dec 22 2018 16:55:01 GMT+0800 (CST)',
       loginsCount: 1,
       lastLogin: 'Fri Dec 07 2018 16:55:01 GMT+0800 (CST)',
       lastIP: '114.93.36.228',
       signedUp: 'Fri Dec 07 2018 16:55:01 GMT+0800 (CST)',
       blocked: false,
       isDeleted: false,
       userLocation: null,
       userLoginHistory: null },
     { _id: '5c08fa74583d9d00019d24ee',
       unionid: 'xxxxxxxxxxxxxxxxx',
       email: null,
       emailVerified: false,
       username: 'xxxxxxxxxxxxxxxxx',
       nickname: 'xxxxxxxxxxxxxxxxx',
       company: '',
       photo: 'xxxxxxxxxxxxxxxxx',
       browser: '',
       registerInClient: 'xxxxxxxxxxxxxxxxx',
       registerMethod: 'oauth:wechat',
       oauth: 'xxxxxxx',
       token: 'xxxxxxx',
       tokenExpiredAt: 'Fri Dec 21 2018 18:31:16 GMT+0800 (CST)',
       loginsCount: 1,
       lastLogin: 'Thu Dec 06 2018 18:31:16 GMT+0800 (CST)',
       lastIP: '221.192.178.75',
       signedUp: 'Thu Dec 06 2018 18:31:16 GMT+0800 (CST)',
       blocked: false,
       isDeleted: false,
       userLocation: null,
       userLoginHistory: null } ],
    totalCount: 2 }
    ```

## 获取用户列表 <a id="&#x83B7;&#x53D6;&#x7528;&#x6237;&#x5217;&#x8868;"></a>

**Authing.list\(page, count\)**

* **参数:**
  * `{Number} page`
    * Default: `1`
  * `{Number} count`
    * Default: `10`
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const userInfo = await authing.list()
        .catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      totalCount: 222,
      list: [{
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
             },
              ...
            ]
    }
    ```

## 删除用户 <a id="&#x5220;&#x9664;&#x7528;&#x6237;"></a>

**Authing.remove\(uid\)**

* **参数:**
  * `{String} uid`
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      const uid = '59e5ff4935eebf1913cfe8a1';
      const userInfo = await authing.remove(uid)
        .catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
    {
        _id: '59e5ff4935eebf1913cfe8a1'
    }
    ```

## 上传头像 <a id="&#x4E0A;&#x4F20;&#x5934;&#x50CF;"></a>

**Authing.selectAvatarFile\(cb\)**

* **参数:**
  * `{function} cb`
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      authing.selectAvatarFile((avatarURL) => {
        // avatarURL 即为头像地址（公网 URL）
      })
    })();
    ```
* **结果:**
  * 此 API 会打开文件选择窗口供用户选择文件，用户选取文件后，系统会自动上传，上传成功后会调用 cb，并把头像 URL 作为参数传入 cb 函数，开发者可将回调函数中的 avatarURL 作为 photo 参数传入 [update](https://docs.authing.cn/#/user_service/update_user) 方法中修改用户头像。

## 修改用户资料 <a id="&#x4FEE;&#x6539;&#x7528;&#x6237;&#x8BBE;&#x7F6E;"></a>

若使用 `JavaScript` 调用，需要使用 `then().catch()` 捕获结果和错误。

**Authing.update\(options\)**

此接口可以用来修改密码、昵称、头像等各种用户信息

* **参数:**
  * `{Object} options`
    * \_id `{String} 必填`
    * email `{String}，选填`
    * emailVerified: `{Boolean}，选填，邮箱是否经过验证`
    * username: `{String}，选填`
    * nickname: `{String}，选填`
    * company: `{String}，选填`
    * oauth: `{String}，选填，oauth 信息`
    * photo: `{String || file object}，选填，用户头像`
    * browser: `{String}，选填，用户注册时所用的浏览器`
    * password: `{String}，选填，用户密码`
    * oldPassword: `{String}（`当有 password 时，旧密码参数必需填写）
    * token: `{String}，选填`
    * tokenExpiredAt: `{String}，选填，token 过期时间`
    * loginsCount: `{Number}，选填，登录次数`
    * lastLogin: `{String}，选填，最后登录时间`
    * lastIP: `{String}，选填，最后登录 IP`
    * signedUp: `{String}，选填，注册时间`
    * blocked: `{Boolean}，选填，是否被锁定`
    * isDeleted: `{Boolean}，选填，是否被删除`
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      // 修改邮箱
      await authing.update({
        _id: "59e5ff4935eebf1913cfe8a1",
        email: 'xxx@xxx.com',
      });
    })();
    ```
* **返回数据:**
  * ```javascript

    {
        "_id": "59e5ff4935eebf1913cfe8a1",
        "email": "xxx@xxx.com",
        "emailVerified": false,
        "username": "premail@premail.com",
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

## 重置密码 <a id="&#x91CD;&#x7F6E;&#x5BC6;&#x7801;"></a>

### **1. 发送验证码 Authing.sendResetPasswordEmail\(options\)**

* **参数:**
  * `{Object} options`
    * email
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      // 发送验证码到指定邮箱
      await authing.sendResetPasswordEmail({
        email: 'xxx@xxx.com',
      });
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "message":"成功",
      "code":null,
      "__typename": "CommonMessage"
    }
    ```

### **2. 验证验证码 Authing.verifyResetPasswordVerifyCode\(options\)**

* **参数:**
  * `{Object} options`
    * email
    * verifyCode
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      // 验证验证码
      await authing.verifyResetPasswordVerifyCode({
        email: 'xxx@xxx.com',
        verifyCode: 'verifyCode',
      });
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "message": "验证成功，请输入新密码",
      "code": null,
      "__typename": "CommonMessage"
    }
    ```

### **3. 发送新密码 Authing.changePassword\(options\)**

* **参数:**
  * `{Object} options`
    * email
    * password
    * verifyCode
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      await authing.changePassword({
        email: 'xxx@xxx.com',
        password: 'new_password',
        verifyCode: 'verifyCode',
      });
    })();
    ```
* **返回数据:**
  * ```javascript
      {
        "_id":"5a2a723d598e37000106786a",
        "email":"1968108962@qq.com",
        "emailVerified":true,
        "username":"1968108962@qq.com",
        "nickname":"",
        "company":"","photo":"http://oxacbp94f.bkt.clouddn.com/authing-avatar.png","browser":"",
        "registerInClient":"59f86b4832eb28071bdd9214","registerMethod":"default:username-password",
        "oauth":"","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiMTk2ODEwODk2MkBxcS5jb20iLCJpZCI6IjVhMmE3MjNkNTk4ZTM3MDAwMTA2Nzg2YSJ9LCJpYXQiOjE1MTQwMjcyNDd9.vWrlzKY-Qr0SXwx8k__BF0ADCBjqGgMWP-wVOWgbH7A","tokenExpiredAt":"Sat Dec 23 2017 19:07:27 GMT+0800 (CST)","loginsCount":1,"lastLogin":"Fri Dec 08 2017 19:07:27 GMT+0800 (CST)","lastIP":"172.20.0.1",
        "signedUp":"Fri Dec 08 2017 19:06:37 GMT+0800 (CST)",
        "blocked":false,"isDeleted":false,
        "__typename":"ExtendUser"
      }
    ```

## 验证用户邮箱 <a id="&#x9A8C;&#x8BC1;&#x90AE;&#x7BB1;"></a>

### **发送验证邮件 Authing.sendVerifyEmail\(options\)**

* **参数:**
  * `{Object} options`
    * email: 需要验证的邮箱
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      await authing.sendVerifyEmail({
        email: 'xxx@xxx.com',
      });
    })();
    ```
* **返回数据:**
  * ```javascript
    {
         message: "发送验证邮件成功", 
         __typename: "CommonMessage"
    }
    ```

发送之后，用户将会收到一个包含验证链接的邮件，当用户打开此链接，我们将进行验证。

## 解绑邮箱 <a id="&#x89E3;&#x7ED1;&#x90AE;&#x7BB1;"></a>

若使用 `JavaScript` 调用，需要使用 `then().catch()` 捕获结果和错误。

**Authing.unbindEmail\(options\)**

* **参数:**
  * `{Object} options`
    * user `{String} 用户 ID，可选，默认为当前登录用户的 ID`
    * client `{String} 应用 ID，可选，默认为当前登录应用的 ID`
* **使用方法:**
  * ```text
    Authing.unbindEmail();
    (async function() {
      const authing = await new Authing({
        clientId: 'your_client_id',
        timestamp: Math.round(new Date() / 1000),
        nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
      });
  
      await authing.unbindEmail();
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
        "isDeleted": false
     }
    ```

**注意事项**

若未绑定其他登录方式, 则不可解绑邮箱

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

{% page-ref page="../../advanced/wxapp-qrcode.md" %}

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

  


