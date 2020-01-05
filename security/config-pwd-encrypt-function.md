---
description: 自定义用户池中用户密码的加密方式
---

# 配置密码加密函数

如果你想自定义密码加密函数，请在此上传函数片段（目前仅支持 Node.js），函数模版请[点击这里下载](http://localhost:5555/faas/template/download)（Authing 不会存储用户的密码原文）。   
  
此功能适用于以下场景：

1. 你将所有用户迁移进了 Authing，但不想让用户修改密码；
2. 你不信任 Authing 的密码加密算法，想使用自己的密码加密算法；

本文档介绍了如何配置密码加密函数。

## 配置步骤

进入用户池，依次点击**配置** -&gt; **密码管理** -&gt; **自定义密码加密方法**，如下图所示：

![](../.gitbook/assets/image%20%28253%29.png)

### 下载模版

点击页面中的「下载模版」下载 Node.js 代码模版，模版代码如下所示：

```javascript
var getRawBody = require('raw-body')

const encryptPassword = password => {
  // 在此编写加密密码的函数

  return password
}

// 模版代码，勿改动
module.exports.encrypt = function(request, response, context) {
  // get request body
  getRawBody(request, function(err, body) {
    const queries = request.queries
    const password = queries.password

    if (!password) {
      response.setStatusCode(500)
      response.setHeader('content-type', 'application/json')
      response.send(
        JSON.stringify(
          {
            message: 'Please provide password via url query',
          },
          null,
          4
        )
      )
    }

    const respBody = {
      password: encryptPassword(password), // 在此加密密码
    }

    response.setStatusCode(200)
    response.setHeader('content-type', 'application/json')
    response.send(JSON.stringify(respBody, null, 4))
  })
}
```

开发者只需要在 `encryptPassword` 函数中编写相应的密码加密方法即可。

### 编写代码

开发者可在 `encryptPassword`函数中编写任意方法，对密码原文加密。

### 引入 NPM 包

若开发者需要引入第三方 NPM 包，请直接使用 NPM 直接安装。

{% hint style="info" %}
NPM 是 Node.js 生态的包管理工具。
{% endhint %}

以下是引入 md5 包的一个代码示例：

```haskell
$ npm install blueimp-md5
```

安装完成后在文件夹内会多出一个 node\_modules 文件夹，之后编写代码：

{% hint style="info" %}
node\_modules  是存储 NPM 包的文件夹。
{% endhint %}

```javascript
var getRawBody = require('raw-body')
var md5 = require('blueimp-md5')

const encryptPassword = password => {
  // 使用 MD5 加密密码
  return md5(password)
}

// 模版代码，勿改动
module.exports.encrypt = function(request, response, context) {
  // get request body
  getRawBody(request, function(err, body) {
    const queries = request.queries
    const password = queries.password

    if (!password) {
      response.setStatusCode(500)
      response.setHeader('content-type', 'application/json')
      response.send(
        JSON.stringify(
          {
            message: 'Please provide password via url query',
          },
          null,
          4
        )
      )
    }

    const respBody = {
      password: encryptPassword(password), // 在此加密密码
    }

    response.setStatusCode(200)
    response.setHeader('content-type', 'application/json')
    response.send(JSON.stringify(respBody, null, 4))
  })
}
```

这份代码将密码经过 MD5 加密后返回。

### 上传函数至服务器

{% hint style="info" %}
Authing 支持的代码包只能为 .js 格式或 .zip 格式。
{% endhint %}

若你没有引入任何包，可直接上传 .js 格式的模版文件；若你引入了包请连带 node\_modules 一起打包为 .zip 格式并在 Authing 控制台中上传。

![](../.gitbook/assets/image%20%28284%29.png)

### 测试密码加密函数

上传成功后开发者可测试密码加密效果，如下所示，在输入框中输入原密码后点击「加密测试」即可看到加密后的密码（若未上传任何加密函数将显示 Authing 默认的密码加密结果）。

![](../.gitbook/assets/image%20%28296%29.png)

## 注意事项

{% hint style="info" %}
密码加密函数上传后即生效，会影响原用户，建议此功能在完全新的用户池中使用。

若你需要在旧用户池中修改密码加密函数，请联系我们：+8618000179176。
{% endhint %}

