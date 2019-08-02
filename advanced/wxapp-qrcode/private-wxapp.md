---
description: 如果你是私有部署「小登录」的用户请查看本章节进行接入。
---

# 接入私有化小程序

本章节针对将小程序部署到自己主体下的用户。

#### 安装 authing-js-sdk

```bash
$ npm install authing-js-sdk --save
```

**初始化**

初始化时使用 **useSelfWxapp** 参数并置为 true：

```javascript
const Authing = require('authing-js-sdk');

// 初始化 Authing SDK for Web
const auth = new Authing({
    clientId: 'your_client_id',
    timestamp: Math.round(new Date() / 1000),
    nonce: Math.ceil(Math.random() * Math.pow(10, 6)),
    useSelfWxapp: true,
});

auth.then(function(authing) {
    // 调用小程序扫码登录的方法，此方法将生成一个用于扫码登录的图片和相关提示信息
    // 用户扫描成功后会回调至开发者在控制台中配置的 Redirect URI
    authing.startWXAppScaning({
      // 可选，登录失败后的回调函数，一般为网络问题
      onError: function(error) {}, 
    });
});
```

{% hint style="info" %}
若不想在扫码登录后发生页面跳转，可以配置 redirect 参数为 false，然后在 onSuccess 函数中获取用户数据后执行相应业务，如：

```javascript
authing.startWXAppScaning({
  // 不自动跳转
  redirect: false,
  
  // 扫码成功
  onSuccess(res) {
    const userInfo = res.data;
    
    // 存储 token 到 localStorage 中
    localStorage.setItem('token', userInfo.token);
  }
});
```

若你想在后端验证 Token 合法性，请参考：[验证 Token 合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token)。
{% endhint %}

