---
description: >-
  多因素身份验证（MFA）是一种安全系统，是为了验证一项操作合法性而实行多种身份验证。例如银行的 U 盾，异地登录要求手机短信验证。阅读本教程后，你可以基于
  Authing 的 MFA 功能进行定制化开发。
---

# 接入 MFA

## 前期准备

1. [注册一个 Authing 账号](https://authing.cn/login)。
2. [创建一个用户池](../quickstart/basic.md#yong-hu-chi)。
3. [创建一个 OIDC 应用](../advanced/oidc/create-oidc.md)。
4. 建议安装 http-server 用于快速启动 http 服务器来查看 html 文件。`npm install -g http-server`

## 所需技能

1. 基础 HTML 知识
2. 中级 Javascript 知识

## 新建一个空白 html 文件用于编写 Authing 程序

本教程只是为了演示，因此我们没选择高级框架，这可以让我们专注于 MFA 逻辑本身。

```markup
<!DOCTYPE html><html lang="en">  <head>    <meta charset="UTF-8" />    <meta name="viewport" content="width=device-width, initial-scale=1.0" />    <meta http-equiv="X-UA-Compatible" content="ie=edge" />    <title>Authing MFA Example</title>  </head>  <body></body></html>
```

## 添加基本控件

为我们的 html 添加四个输入框和三个按钮。用于输入用户名、密码、验证码、MFA 口令等信息。我们使用 jquery 的插件来生成 MFA 令牌的二维码。

```markup
<div class="row">  <label>用户名：<input id="username"/></label>  <label>密码：<input id="password"/></label>  <label>验证码：<input id="verify-code"/><img id="verify-img" style="vertical-align: bottom;"/></label></div><div id="mfa" style="visibility: hidden;" class="row">  <label>MFA 口令：<input type="number" id="mfa-code"/></label></div><div class="row">  <input type="button" value="登录" id="login" onclick="handleLogin()" />  <input    type="button"    value="注册"    id="register"    onclick="handleRegister()"  />  <input    type="button"    value="开启 MFA"    id="register"    onclick="handleChangeMFA()"  /></div><div id="qrcode" class="row"></div><div id="qrcode-info" style="visibility: hidden;" class="row">  请使用 MFA 令牌管理工具扫码添加令牌</div><script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js"></script><script src="https://cdn.bootcss.com/jquery.qrcode/1.0/jquery.qrcode.min.js"></script><script src="https://cdn.jsdelivr.net/npm/authing-js-sdk"></script>
```

## 初始化 SDK

**浏览器端初始化时需传入三个参数**

* **clientId**
  * 应用 ID，可从 [Authing 控制台](https://authing.cn/dashboard)中[获取](https://docs.authing.cn/#/quick_start/howto)。
* **timestamp**
  * 当前时间戳 `Math.round(new Date() / 1000)`
* **nonce**
  * 一个随机数字，不要超过十位数

```markup
<script>const auth = new Authing({	clientId: 'your_client_id',	timestamp: Math.round(new Date() / 1000),	nonce: Math.ceil(Math.random() * Math.pow(10, 6)),});auth.then((authing) => {	// authing.login	// authing.register	// ...	window.validAuth = authing;});</script>
```

## 创建一个用户

将此函数绑定到登录按钮的点击事件上，用随机信息注册一个用户。

```javascript
// 使用随机信息注册一个用户async function handleRegister() {  let rand = Math.random()    .toString(36)    .slice(2);  let res = await validAuth.register({    email: rand + "@test.com",    password: "123456"  });  alert(JSON.stringify(res));  document.getElementById("username").value = res.email;  document.getElementById("password").value = "123456";  window.userId = res._id;}
```

## 开启指定用户的 MFA

操作用户的 MFA 开启状态之前，必须要先获得用户的 token。可以通过 `validAuth.login` 函数以用户的身份登录，或者直接使用用户的 token 初始化。

```javascript
// 开启用户的 MFAasync function handleChangeMFA() {  let username = document.getElementById("username").value;  let password = document.getElementById("password").value;  let verifyCode = document.getElementById("verify-code").value;  try {    let res = await validAuth.login({      email: username,      password: password,      verifyCode: verifyCode    });    // 或者使用用户的 token 初始化    // authing.initUserClient('用户的 JWT token');    let mfa = await validAuth.changeMFA({      userId: res._id,      userPoolId: "5c95905578fce5000166f853",      enable: true    });    alert(JSON.stringify(mfa));    jQuery("#qrcode").qrcode(      `otpauth://totp/Authing:Test?secret=${mfa.shareKey}&period=30&digits=6&issuer=Authing`    );    document.getElementById("qrcode-info").style.visibility = "visible";  } catch (err) {    alert("失败消息：" + JSON.stringify(err));  }}
```

## 生成 MFA 令牌二维码

社区有许多可以生成二维码的库，这里使用了 jquery 的二维码插件生成 MFA 二维码。

```javascript
jQuery("#qrcode").qrcode(  `otpauth://totp/Authing:Test?secret=${mfa.shareKey}&period=30&digits=6&issuer=Authing`);
```

## 处理用户登录逻辑

当调用 `validAuth.login` 函数进行登录时，如果检测到用户开启了 MFA 二次验证，而没有携带 MFA 口令，会抛出一个错误，错误代码为 1635。在错误处理函数中，携带 MFA 口令再次调用 `validAuth.login` 即可。

```javascript
// 登录逻辑async function handleLogin() {  // validAuth.login({email: })  let username = document.getElementById("username").value;  let password = document.getElementById("password").value;  let verifyCode = document.getElementById("verify-code").value;  if (isMFAShow) {    let MFACode = document.getElementById("mfa-code").value;    try {      let res = await validAuth.login({        email: username,        password: password,        MFACode: MFACode,        verifyCode: verifyCode      });      alert("成功消息：" + JSON.stringify(res));    } catch (err) {      alert("失败消息：" + JSON.stringify(err));      if(err.message.code === 2000 || err.message.code === 2001 || err.message.code === 2006) {        console.log(err.message.data.url)        document.getElementById("verify-img").src = err.message.data.url      }    }  } else {    try {      let res = await validAuth.login({        email: username,        password: password,        verifyCode: verifyCode      });      alert("成功消息：" + JSON.stringify(res));    } catch (err) {      alert("失败消息：" + JSON.stringify(err));      if (err.message.code === 1635) {        showMFAInput();      }      if(err.message.code === 2000 || err.message.code === 2001 || err.message.code === 2006) {        console.log(err.message.data.url)        document.getElementById("verify-img").src = err.message.data.url      }    }  }}
```

## 完整代码

Github: [mfa-demo](https://github.com/authing/mfa-demo)

## 运行方法

双击打开 index.html 文件。

或在项目目录启动一个 http 服务器。

```bash
$ npm install -g http-server$ http-server
```

然后访问 127.0.0.1:8080。

