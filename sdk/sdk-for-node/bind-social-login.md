# 绑定社会化登录

## 绑定社会化账号

**Authing.bindOAuth\(options\)**

* **参数:**
  * `{Object} options`
    * type `{String} 必填, 暂时只支持github, wechat`
    * unionid `{String} 必填，OAuth返回的用户信息中的唯一ID`
    * userInfo `{String} 必填, JSON.strify(OAuth返回的用户信息)`
    * uesr `{String} 用户ID，可选，默认为当前登录用户的ID`
    * client `{String} 应用ID，可选，默认为当前登录应用的ID`
* **使用方法:**
  * ```javascript
    (async function() {  const authing = new Authing({    userPoolId: 'your_userpool_id',    secret: 'your_userpool_secret'  });  const bindResult = await authing.bindOAuth({    type: 'github',    unionid: '12345678',    userInfo: "{"login":"demo","id":12345678,"node_id":"demodemodemo","avatar_url":"https://avatars1.githubusercontent.com/u/19266401?v=4","gravatar_id":"","url":"https://api.github.com/users/demo","html_url":"https://github.com/demo","followers_url":"https://api.github.com/users/demo/followers","following_url":"https://api.github.com/users/demo/following{/other_user}","gists_url":"https://api.github.com/users/demo/gists{/gist_id}","starred_url":"https://api.github.com/users/demo/starred{/owner}{/repo}","subscriptions_url":"https://api.github.com/users/demo/subscriptions","organizations_url":"https://api.github.com/users/demo/orgs","repos_url":"https://api.github.com/users/demo/repos","events_url":"https://api.github.com/users/demo/events{/privacy}","received_events_url":"https://api.github.com/users/demo/received_events","type":"User","site_admin":false,"name":"demo","company":null,"blog":"","location":null,"email":"demo@gmail.com","hireable":null,"bio":"A web developer.","public_repos":10,"public_gists":0,"followers":2,"following":3,"created_at":"2016-05-09T12:43:11Z","updated_at":"2018-07-23T04:42:08Z"}"  });})();
    ```
* **返回数据:**
  * ```javascript
    {    "_id": "aeolkjhdhfhkkjdsfj",    "user": "dfdfdsfdsfdsfdsfds",    "client": "dsfjdskfjkldsjfklj",    "type": "github",    "userInfo": "{"login":"demo","id":12345678,"node_id":"demodemodemo","avatar_url":"https://avatars1.githubusercontent.com/u/19266401?v=4","gravatar_id":"","url":"https://api.github.com/users/demo","html_url":"https://github.com/demo","followers_url":"https://api.github.com/users/demo/followers","following_url":"https://api.github.com/users/demo/following{/other_user}","gists_url":"https://api.github.com/users/demo/gists{/gist_id}","starred_url":"https://api.github.com/users/demo/starred{/owner}{/repo}","subscriptions_url":"https://api.github.com/users/demo/subscriptions","organizations_url":"https://api.github.com/users/demo/orgs","repos_url":"https://api.github.com/users/demo/repos","events_url":"https://api.github.com/users/demo/events{/privacy}","received_events_url":"https://api.github.com/users/demo/received_events","type":"User","site_admin":false,"name":"demo","company":null,"blog":"","location":null,"email":"demo@gmail.com","hireable":null,"bio":"A web developer.","public_repos":10,"public_gists":0,"followers":2,"following":3,"created_at":"2016-05-09T12:43:11Z","updated_at":"2018-07-23T04:42:08Z"}",    "unionid": "12345678",    "createdAt": "2016-05-09T12:43:11Z"}
    ```

## 获取用户已经绑定的社会化账号

**Authing.readUserOAuthList\(options\)**

* **参数:**
  * `{Object} options`
    * uesr `{String} 用户 ID，可选，默认为当前登录用户的 ID`
    * client `{String} 应用 ID，可选，默认为当前登录应用的 ID`
* **使用方法:**
  * ```javascript
    (async function() {  const authing = new Authing({    userPoolId: 'your_userpool_id',    secret: 'your_userpool_secret'  });  const userBindedOAuthList = await authing.readUserOAuthList();})();
    ```
  * 如果 binded 为 true 则代表已经绑定
  * 若 binded 为 false , 若按[此方法](/oauth/oauth?id=不需要编写后端代码的-oauth-调用方法（推荐使用）)配置, 使用户访问oAuthUrl则可开始OAuth绑定流程
* **返回数据:**
  * ```javascript
    [{    "type":"github",    "oAuthUrl":"https://github.com/login/oauth/authorize/?client_id=demo&state=demo",    "image":"https://usercontents.authing.cn/oauth/logo/github.svg",    "name":"Github登录",    "binded":true},{    "type":"wechat",    "oAuthUrl":"https://open.weixin.qq.com/connect/qrconnect?appid=demo&redirect_uri=demo&response_type=code&scope=snsapi_login&state=demo",    "image":"https://usercontents.authing.cn/oauth/logo/wechat.png",    "name":"微信登录",    "binded":false}]
    ```

**注意事项**

暂不支持小程序绑定

## 解绑社会化登录账号

**Authing.unbindOAuth\(options\)**

* **参数:**
  * `{Object} options`
    * type `{String} 必选, 暂时只支持 github, wechat`
    * uesr `{String} 用户 ID，可选，默认为当前登录用户的 ID`
    * client `{String} 应用 ID，可选，默认为当前登录应用的 ID`
* **使用方法:**
  * ```javascript
    (async function() {  const authing = new Authing({    userPoolId: 'your_userpool_id',    secret: 'your_userpool_secret'  });  const unbindedResult = await authing.unbindOAuth({    type: 'github'  });})();
    ```
* **返回数据:**
  * ```javascript
    {    "_id": "aeolkjhdhfhkkjdsfj",    "user": "dfdfdsfdsfdsfdsfds",    "client": "dsfjdskfjkldsjfklj",    "type": "github",    "userInfo": "{"login":"demo","id":12345678,"node_id":"demodemodemo","avatar_url":"https://avatars1.githubusercontent.com/u/19266401?v=4","gravatar_id":"","url":"https://api.github.com/users/demo","html_url":"https://github.com/demo","followers_url":"https://api.github.com/users/demo/followers","following_url":"https://api.github.com/users/demo/following{/other_user}","gists_url":"https://api.github.com/users/demo/gists{/gist_id}","starred_url":"https://api.github.com/users/demo/starred{/owner}{/repo}","subscriptions_url":"https://api.github.com/users/demo/subscriptions","organizations_url":"https://api.github.com/users/demo/orgs","repos_url":"https://api.github.com/users/demo/repos","events_url":"https://api.github.com/users/demo/events{/privacy}","received_events_url":"https://api.github.com/users/demo/received_events","type":"User","site_admin":false,"name":"demo","company":null,"blog":"","location":null,"email":"demo@gmail.com","hireable":null,"bio":"A web developer.","public_repos":10,"public_gists":0,"followers":2,"following":3,"created_at":"2016-05-09T12:43:11Z","updated_at":"2018-07-23T04:42:08Z"}",    "unionid": "12345678",    "createdAt": "2016-05-09T12:43:11Z"}
    ```

