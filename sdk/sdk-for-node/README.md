# SDK for Node.js

Node SDK 用于 Node 环境下，可以用于管理用户池中所有用户，拥有全部操作权限。

Github：[https://github.com/Authing/authing.js](https://github.com/Authing/authing.js)。

## 安装

**NPM**

当构建大规模应用时，我们推荐使用 `npm` 进行安装。

```bash
# latest stable
$ npm install authing-js-sdk --save
```

## 初始化

先从 [Authing 控制台](https://authing.cn/dashboard) 中 [获取用户池 ID 和 Secret](https://learn.authing.cn/authing/others/faq#ru-he-huo-qu-client-id-he-client-secret)。

初始化代码：

```javascript
const auth = new Authing({
	userPoolId: 'your_userpool_id',
	secret: 'your_userpool_secret'
});
// 初始化之后可以直接使用相关代码
auth.register({...}).then((info) => {

});
auth.login({...}).then((info) => {

});
```

#### 其他参数

* **preflight**
  * 是否开启网络状况预检，默认为 false。此参数适用于检查用户的网络是否屏蔽了 authing.cn 这个域名（某些企业的内网会屏蔽这个域名），检查成功不进行任何通知，检查失败后会调用传入的错误处理函数。**执行预检之后会导致 SDK 初始化速度变慢，请谨慎使用。**
* **cdnPreflight**
  * 是否开启 CDN 网络状况预检，默认为 false。此参数适用于检查用户的网络是否可以访问七牛云 CDN（某些开了代理的场景下无法访问），检查成功不进行任何通知，检查失败后会调用传入的错误处理函数。**执行 CDN 预检之后会导致 SDK 初始化速度变慢，请谨慎使用。**
* **timeout**
  * 超时时间，默认为 10000 毫秒（10 秒）。
* **onInitError，**`function(err) {}`
  * 错误处理函数，用于处理初始化失败错误、预检错误。

## 使用

Authing SDK 的所有 API 都支持 **Promise**。

```javascript
const Authing = require('authing-js-sdk');

const authing = new Authing({
	userPoolId: 'your_userpool_id',
	secret: 'your_userpool_secret'
});

authing.login({
	email: 'test@testmail.com',
	password: 'testpassword'
}).then(function(user) {
	console.log(user);	
}).catch(function(error) {
	console.log(error);	
});
```

如果你使用 `ES6+` 推荐用 `await` 处理异步，示例如下：

```javascript
const Authing = require('authing-js-sdk');

const main = async () => {

	let auth = new Authing({
		userPoolId: 'your_client_id',
		secret: 'your_userpool_secret'
	});

	let user;

	//使用async时需要使用 try...catch... 捕捉错误
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
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
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
    * **MFACode，**可选，如果用户开启了 MFA 会要求输入 6 位动态口令，返回数据会出现 1635 错误代码
    * **lastIP，**可选，若连续出现验证码验证失败情况，请将客户端 IP 填入
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
      });
  
      const userInfo = await authing.login({
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

此接口可结合[使用手机验证码登录](./#shi-yong-shou-ji-yan-zheng-ma-deng-lu)使用。

**Authing.getVerificationCode\(phone\)**

* **参数:**
  * `{String} phone`
  * 手机号
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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

验证码接口可结合[使用手机验证码登录](./#shi-yong-shou-ji-yan-zheng-ma-deng-lu)使用。

## 使用手机验证码登录

**Authing.loginByPhoneCode\(options\)**

* **参数:**
  * `{Object} options`
    * **phone**，必填，手机号
    * **phoneCode**，必填，使用发送短信接口获取
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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

LDAP 服务的配置流程请参考[配置 LDAP 服务](../../advanced/ldap.md)。

**Authing.loginByLDAP\(options\)**

* **参数:**
  * `{Object} options`
    * `username`，在 LDAP 服务中的登录名，可以是邮箱或用户名
    * `password`，在 LDAP 服务中的密码
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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

## 验证用户 Token

**Authing.checkLoginStatus\(options\)**

* **参数:**
  * `{Object} options`
    * **user**，必填，用户的 \_id
* **使用方法:**
  * ```javascript
    (async () => {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
      });
    
      const result = await authing.checkLoginStatus('USER_JWT_TOKEN');
    })()
    ```
* **返回数据:**

若 Token 合法，则返回数据为：

```javascript
{
  status: false,
  code: 200,
  message: '已登录',
  token: {
    ... // Token 数据
  }
}
```

当 status 为 false 时，有三种情况，分别返回：

```javascript
{
  status: false,
  code: 2020,
  message: '未登录'
}
```

```javascript
{
  status: false,
  code: 2206,
  message: '登录信息已过期' 
}
```

```javascript
{
  status: false,
  code: 2207,
  message: '登录信息有误'
}
```

## 刷新用户 Token

**Authing.refreshToken\(options\)**

* **参数:**
  * `{Object} options`
    * **user**，必填，用户的 \_id
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
      });
  
      const token = await authing.refreshToken({
        user: '59e5ff4935eebf1913cfe8a1'
      })
        .catch((error) => { ... })
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoieGlleWFuZ0Bkb2RvcmEuY24iLCJpZCI6IjVkMDQ2M2FjODdkNmU5NTEwM2E3MTBjNSJ9LCJpYXQiOjE1NjA1NzEwNzYsImV4cCI6MTU2MTg2NzA3Nn0.qs-Q7UOC5wFa40uIau-50-VjSAVOVjr5nw2opzQeLYg",
      "iat": 1560571076,
      "exp": 1561867076
    }
    ```

## 退出

**Authing.logout\(uid\)**

* **参数:**
  * `{String} uid`，必填，用户的 \_id
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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

{% page-ref page="update-user-permissions.md" %}

## 获取单个用户资料

**Authing.user\(options\)**

* **参数:**
  * `{Object} options`
    * **id**，必填，用户的 \_id
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
      });
  
      const userInfo = await authing.user({
        id: '59e5ff4935eebf1913cfe8a1'
      })
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
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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

## 修改密码

**Authing.update\(options\)**

* **参数:**
  * `{Object} options`
    * password: `{String}，必填，用户新密码`
    * oldPassword: `{String}，选填，用户原始密码`
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id'
      });
      // 以用户身份登录
      const login = await authing.login({
        email: 'test@test.com',
        password: '123456'
      })
      // 修改邮箱
      await authing.update({
        _id: login._id,
        password: '123456',
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

## 修改用户资料 <a id="&#x4FEE;&#x6539;&#x7528;&#x6237;&#x8BBE;&#x7F6E;"></a>

{% hint style="info" %}
此接口不能用来直接修改手机号和邮箱！

修改手机号请使用  changePhone 接口。

修改邮箱请使用 changeEmail 接口。
{% endhint %}

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
    * phone: `{String}，选填`
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
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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

## 修改邮箱

如果用户已经绑定了邮箱，默认情况下，需要同时验证原有邮箱（目前账号绑定的邮箱）和当前邮箱（将要绑定的邮箱）。也就是说，用户 A 当前绑定的邮箱为 123456@qq.com，想修改为 1234567@qq.com，那么就需要同时验证这两个邮箱。

开发者也可以选择不开启 “验证原有邮箱“ ，可以在 [Authing 控制台](https://authing.cn/dashboard) 的 **基础配置** - **基础设置** 页面的**安全设置**模块进行关闭。如下图所示：

![](../../.gitbook/assets/image%20%2852%29.png)

> 验证码通过的 sendChangeEmailVerifyCode 接口发送。

**Authing.updateEmail\(options\)**

* 参数：
  * {Object} options
    * email : 当前邮箱，必填。
    * emailCode: 当前邮箱验证码，必填。
    * oldEmail：原有邮箱，选填。
    * oldEmailCode: 原有邮箱验证码，选填。
* 使用方法

```javascript
(async function() {
  const authing = new Authing({
    userPoolId: 'your_userpool_id'
  });
  // 以用户身份登录
  const login = await authing.login({
    email: 'test@test.com',
    password: '123456'
  })
  await authing.updateEmail({
	email: "YOUR_NEW_EMAIL"
	emailCode: "YOUR_EMAIL_CODE"
  });
})();
```

* 返回数据：最新的用户信息

```javascript
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

##  发送修改邮箱验证码

开发者可以在 **消息服务 - 邮件模版** 中设置邮件模板，默认的邮件如下图所示：

![](../../.gitbook/assets/image%20%2825%29.png)

开发者可以自定义 CSS 样式、主题等。

注意：如果请求发送的邮箱已经注册过了，会提示 “该邮箱已绑定，请换一个吧“ 错误。

**Authing.sendChangeEmailVerifyCode\(options\)**

* 参数：
  * `{Object} options`
    * `email`
* 使用方法

```javascript
(async function() {
  const authing = new Authing({
    userPoolId: 'your_userpool_id'
  });
  // 以用户身份登录
  const login = await authing.login({
    email: 'test@test.com',
    password: '123456'
  })
  await authing.sendChangeEmailVerifyCode({
	email: "YOUR_NEW_EMAIL"
  });
})();
```

* 返回数据

成功示例

```javascript
{
    code: 200,
    message: "成功"
}
```

失败示例：

```javascript
{
    code: 2035,
    message: "该邮箱已绑定，请换一个吧"
}
```

## 修改手机号

和修改邮箱一样，默认情况下，如果用户当前已经绑定了手机号，需要同时验证原有手机号（目前账号绑定的手机号）和当前邮箱（将要绑定的手机号）。也就是说，用户 A 当前绑定的手机号为 15888888888，想修改为 15899999999，那么就需要同时验证这两个手机号。

开发者也可以选择不开启 “验证原有手机号“ ，可以在 [Authing 控制台](https://authing.cn/dashboard) 的 **基础配置** - **基础设置** 页面的**安全设置**模块进行关闭。如下图所示：

![](../../.gitbook/assets/image%20%28211%29.png)

> 手机号短信验证码通过  **getVerificationCode** 接口发送**。**

**Authing.updatePhone\(options\)**

* 参数：
  * {Object} options
    * phone : 当前手机号，必填。
    * emailCode: 当前手机号短信验证码，必填。
    * oldPhone：原有手机号，选填。
    * oldPhoneCode: 原有手机号短信验证码，选填。
* 使用方法

```javascript
(async function() {
  const authing = new Authing({
    userPoolId: 'your_userpool_id'
  });
  // 以用户身份登录
  const login = await authing.login({
    email: 'test@test.com',
    password: '123456'
  })
  await authing.updateEmail({
	phone: "YOUR_NEW_PHONE_NUMBER"
	phoneCode: "YOUR_PHONE_CODE",
  oldPhone: "YOUR_CURRENT_PHONE_NUMBER",
  oldPhoneCode: "YOUR_CURRENT_PHONE_CODE"
  });
})();
```

* 返回数据：最新的用户信息。

##  <a id="&#x91CD;&#x7F6E;&#x5BC6;&#x7801;"></a>

## 重置密码 <a id="&#x91CD;&#x7F6E;&#x5BC6;&#x7801;"></a>

### **1. 发送验证码 Authing.sendResetPasswordEmail\(options\)**

* **参数:**
  * `{Object} options`
    * email
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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
  * ```javascript
    Authing.unbindEmail();
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
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

## 查询用户授权的 SSO 应用列表 <a id="&#x9A8C;&#x8BC1;&#x90AE;&#x7BB1;"></a>

该接口返回用户在一个用户池下授权过的 OAuth 和 OIDC 应用列表。

### **Authing.**getAuthedAppList**\(options\)**

* **参数:**
  * `{Object} options`
    * **clientId**: 用户池 id，必传
    * **userId**：用户 id，必传
    * **page**：页数，可选，默认 1
    * **count**：每页数目，可选，默认 10
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
      });
  
      await authing.getAuthedAppList({
      	clientId: "5d023c7b5642461234b9c62e",
    	userId: "5d765d4013d73a5e90b7857a",
    	page: 1,
    	count: 10
      });
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "OAuthApps": [
        {
          "_id": "5d5a8a7bbc7275af2cb71920",
          "name": "test1",
          "domain": "test123",
          "clientId": "5d5921ffaa016518f658498d",
          "description": "",
          "isDeleted": false,
          "grants": [
            "authorization_code",
            "implicit",
            "refresh_token"
          ],
          "redirectUris": [
            "http://qq.com"
          ],
          "when": "Mon Aug 19 2019 19:39:39 GMT+0800 (CST)"
        },
        {
          "_id": "5d5e2e8b026f9d145bf89203",
          "name": "oauth1",
          "domain": "oauth1",
          "clientId": "5d5921ffaa016518f658498d",
          "description": "",
          "isDeleted": false,
          "grants": [
            "authorization_code",
            "implicit",
            "refresh_token"
          ],
          "redirectUris": [
            "http://qq.com"
          ],
          "when": "Thu Aug 22 2019 13:56:27 GMT+0800 (CST)"
        }
      ],
      "OIDCApps": [
        {
          "_id": "5d5a85e258375a32d1133d45",
          "name": "test1",
          "client_id": "5d5a85e258375a32d1133d45",
          "domain": "test1",
          "description": "test1",
          "authorization_code_expire": "600",
          "when": "Mon Aug 19 2019 19:20:02 GMT+0800 (CST)",
          "isDeleted": false,
          "id_token_signed_response_alg": "HS256",
          "response_types": [
            "code"
          ],
          "grant_types": [
            "authorization_code",
            "refresh_token"
          ],
          "token_endpoint_auth_method": "client_secret_post",
          "redirect_uris": [
            "http://qq.com"
          ],
          "image": "https://usercontents.authing.cn/client/logo@2.png",
          "access_token_expire": "3600",
          "id_token_expire": "3600",
          "cas_expire": "3600"
        }
      ],
      "totalCount": 3
    }
    ```

## 撤回用户对 SSO 应用的授权 <a id="&#x9A8C;&#x8BC1;&#x90AE;&#x7BB1;"></a>

### **Authing.revokeAuthedApp\(options\)**

* **参数:**
  * `{Object} options`
    * **userPoolId**: 用户池 id，必传
    * **userId**：用户 id，必传
    * **appId**：SSO 应用的 id，必传
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
      	secret: 'your_userpool_secret'
      });
  
      await authing.revokeAuthedApp({
      	userPoolId: "5d023c7b5642461234b9c62e",
    	userId: "5d765d4013d73a5e90b7857a",
    	appId: "5d5e2e8b026f9d145bf89203"
      });
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "isRevoked": "true",
      "_id": "5d7661e17a9f981da5fa8098",
      "scope": "profile",
      "appId": "5d5e2e8b026f9d145bf89203",
      "userId": "5d765d4013d73a5e90b7857a",
      "type": "oauth"
    }
    ```

## 自定义请求链接

`Authing` 构造函数包含一个名为 `host` 对象，可接收自定义的请求链接（适合私有部署 Authing 的用户使用），使用方法如下：

```javascript
const auth = new Authing({
	userPoolId: 'xxxx',
	secret: 'xxx',
	host: {
		user: 'https://users.authing.cn/graphql',
		oauth: 'https://oauth.authing.cn/graphql'
	}
});
```

`host` 对象中 `user` 和 `oauth` 的默认值是 Authing 官方的请求链接：

* [https://users.authing.cn/graphql](https://users.authing.cn/graphql)
* [https://oauth.authing.cn/graphql](https://oauth.authing.cn/graphql)

  

