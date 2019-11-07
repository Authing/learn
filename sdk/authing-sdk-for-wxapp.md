# SDK for 微信小程序

相关接口和 SDK for Web 版本相同：

{% page-ref page="sdk-for-web/" %}

Github 地址：

{% embed url="https://github.com/Authing/authing-wxapp-sdk" %}

{% hint style="info" %}
**NOTE!** 在小程序中开发需要在小程序管理后台中配置域名，两个域名分别为：`https://users.authing.cn`和`https://oauth.authing.cn`
{% endhint %}

## **下载代码**

```bash
$ git clone https://github.com/Authing/authing-wxapp-sdk
```

## **引入文件**

然后将下载的代码内的 authing 文件夹移动到你的项目目录下，之后使用 require 引入

```javascript
var Authing = require('path/to/authing/authing.js')
```

## **使用**

注意，在使用小程序 SDK 时，可以不传入 email 和 password 参数，取而代之的是 unionid，就是从小程序获取的 openid 或 unionid。

```javascript
var auth = new Authing({	clientId: 'your_client_id',	secret: 'your_app_secret'});auth.then(function(validAuth) {	//验证成功后返回新的 authing-js-sdk 实例(validAuth)，可以将此实例挂在全局	validAuth.login({		unionid: 'test@testmail.com',	}).then(function(user) {		console.log(user);		}).catch(function(error) {		console.log(error);		});	}).catch(function(error) {	//验证失败	console.log(error);});
```

