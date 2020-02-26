# Web 端 SDK

APP 扫码 Web 端用到的 SDK 为 authing-js-sdk，请先了解如何在浏览器端安装以及初始化 SDK。

{% page-ref page="../../sdk/sdk-for-javascript/" %}

{% hint style="info" %}
Authing SDK 提供两种方式接入：

1. 直接使用 Authing 提供的扫码登录组件（startAppAuthScanning）。
2. 只调用业务接口，自己实现 UI。
{% endhint %}

## 一键生成扫码登录组件

Authing.startAppAuthScanning\(options\)

```javascript
authing.startAppAuthScanning({
    mount: '', // 可选，二维码挂载点，如不写则默认漂浮在文档中间
    interval: 1000, // 可选，轮询间隔时间，默认为 800 ms 

    onPollingStart: (intervalNum) => {},
    onResult: (res) => {},
    onScanned: (userInfo) => {},
    onSuccess: (data) => {
        const { ticket, userInfo } = data;
    },
    onCancel: () => {},
    onExpired: () => {},
    onError: (data) => {},

    onQRCodeShow: (qrcode) => {},
    onQRCodeLoad: (qrcode) => {},
    onQRCodeLoadFaild: (error) => {},
    
    tips: '使用 <strong> APP </strong> 扫码登录',
    scannedTips: '用户已扫码，等待确认',
    canceledTips: '用户取消授权',
    expiredTips: '二维码已过期',
    successTips: '扫码成功',
    retryTips: '重试',
    failedTips: '网络出错，请重试'
})
```



<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;</th>
      <th style="text-align:left">&#x53EF;&#x9009;/&#x5FC5;&#x9009;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">mount &lt;string&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x6302;&#x8F7D;&#x70B9; Dom ID&#xFF0C;&#x5982;&#x4E0D;&#x5199;&#x5219;&#x9ED8;&#x8BA4;&#x6F02;&#x6D6E;&#x5728;&#x6587;&#x6863;&#x4E2D;&#x95F4;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">interval &lt;number&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x8F6E;&#x8BE2;&#x65F6;&#x95F4;&#x95F4;&#x9694;&#xFF0C;&#x5355;&#x4F4D;&#x4E3A;
        ms&#xFF0C;&#x9ED8;&#x8BA4;&#x4E3A; 800 ms&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">onPollingStart &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">
        <p>&#x8F6E;&#x8BE2;&#x5F00;&#x59CB;&#x65F6;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x4F1A;&#x56DE;&#x8C03;&#x4E00;&#x6B21;</b>&#x3002;&#x56DE;&#x8C03;&#x53C2;&#x6570;
          intervalNum &#x4E3A; setInterval &#x8FD4;&#x56DE;&#x7684;&#x6570;&#x503C;&#xFF0C;&#x53EF;&#x4F7F;&#x7528;
          clearInterval &#x505C;&#x6B62;&#x8F6E;&#x8BE2;&#x3002;</p>
        <p>&#x5982;&#xFF1A;clearInterval(intervalNum)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onResult &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">
        <p>&#x6BCF;&#x6B21;&#x67E5;&#x8BE2;&#x83B7;&#x53D6;&#x5230;&#x6570;&#x636E;&#x90FD;&#x4F1A;&#x56DE;&#x8C03;&#xFF0C;&#x53C2;&#x6570;
          res &#x793A;&#x4F8B;&#x5982;&#x4E0B;&#xFF1A;</p>
        <p><code>{    &quot;code&quot;: 200,    &quot;message&quot;: &quot;&#x67E5;&#x8BE2;&#x4E8C;&#x7EF4;&#x7801;&#x72B6;&#x6001;&#x6210;&#x529F;&quot;,    &quot;data&quot;: {        &quot;qrcodeId&quot;: &quot;5e05f6027fde537d950f7da9&quot;,        &quot;scanned&quot;: false,        &quot;expired&quot;: false,        &quot;success&quot;: false,        &quot;canceled&quot;: false,        &quot;status&quot;: 0,        &quot;userInfo&quot;: {},        &quot;description&quot;: &quot;&#x4E8C;&#x7EF4;&#x7801;&#x8FD8;&#x6CA1;&#x6709;&#x88AB;&#x626B;&#x63CF;&quot;    }}</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onScanned &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x626B;&#x7801;&#x65F6;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x4F1A;&#x56DE;&#x8C03;&#x4E00;&#x6B21;</b>&#x3002;&#x56DE;&#x8C03;&#x53C2;&#x6570;
        userInfo &#x53EA;&#x5305;&#x542B;&#x4E86;&#x7528;&#x6237;&#x6635;&#x79F0;&#x548C;&#x5934;&#x50CF;&#xFF0C;&#x5F00;&#x53D1;&#x8005;&#x53EF;&#x4EE5;&#x5C06;&#x5176;&#x5C55;&#x793A;&#x5728;&#x626B;&#x7801;&#x6846;&#x4E2D;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">onSuccess &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x540C;&#x610F;&#x6388;&#x6743;&#x4E4B;&#x540E;&#x5C06;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x4F1A;&#x56DE;&#x8C03;&#x4E00;&#x6B21;</b>&#xFF0C;<b>&#x4E4B;&#x540E;&#x8F6E;&#x8BE2;&#x7ED3;&#x675F;</b>&#x3002;&#x53C2;&#x6570;
        data &#x662F;&#x4E00;&#x4E2A;&#x5B57;&#x5178;&#xFF0C;&#x5305;&#x542B;&#x4E24;&#x4E2A;&#x5B57;&#x6BB5;&#xFF1A;ticket
        &#x548C; userInfo&#x3002;&#x51FA;&#x4E8E;&#x5B89;&#x5168;&#x6027;&#x8003;&#x8651;&#xFF0C;&#x9ED8;&#x8BA4;&#x60C5;&#x51B5;&#x4E0B;&#xFF0C;userInfo
        &#x53EA;&#x4F1A;&#x5305;&#x542B;&#x6635;&#x79F0;&#xFF08;nickname&#xFF09;&#x548C;&#x5934;&#x50CF;&#xFF08;photo&#xFF09;&#x4E24;&#x4E2A;&#x5B57;&#x6BB5;&#xFF0C;&#x5F00;&#x53D1;&#x8005;&#x4E5F;&#x53EF;&#x4EE5;&#x5728;&#x540E;&#x53F0;&#x914D;&#x7F6E;&#x4F7F;&#x5176;&#x8FD4;&#x56DE;&#x5B8C;&#x6574;&#x7528;&#x6237;&#x4FE1;&#x606F;&#xFF0C;&#x8BE6;&#x60C5;&#x89C1;&#x81EA;&#x5B9A;&#x4E49;&#x914D;&#x7F6E;&#x3002;
        ticket &#x53EF;&#x4EE5;&#x7528;&#x6765;&#x6362;&#x53D6;&#x5B8C;&#x6574;&#x7684;&#x7528;&#x6237;&#x4FE1;&#x606F;&#xFF0C;&#x76F8;&#x5173;&#x63A5;&#x53E3;&#x89C1;&#x4E0B;&#x6587;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">onCancel &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x53D6;&#x6D88;&#x6388;&#x6743;&#x53EA;&#x4F1A;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x56DE;&#x8C03;&#x4E00;&#x6B21;&#xFF0C;&#x4E4B;&#x540E;&#x8F6E;&#x8BE2;&#x7ED3;&#x675F;&#x3002;</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onExpired &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x4E8C;&#x7EF4;&#x7801;&#x5931;&#x6548;&#x65F6;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x56DE;&#x8C03;&#x4E00;&#x6B21;&#xFF0C;&#x4E4B;&#x540E;&#x8F6E;&#x8BE2;&#x7ED3;&#x675F;&#x3002;</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onError &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x6BCF;&#x6B21;&#x67E5;&#x8BE2;&#x5931;&#x8D25;&#x65F6;&#x90FD;&#x4F1A;&#x56DE;&#x8C03;&#x3002;&#x56DE;&#x8C03;&#x53C2;&#x6570;
        data &#x793A;&#x4F8B;&#x5982; {&quot;code&quot;: 2241,&quot;message&quot;:
        &quot;&#x4E8C;&#x7EF4;&#x7801;&#x4E0D;&#x5B58;&#x5728;&quot;,&quot;data&quot;:
        null}<b>&#x3002;</b>&#x5B8C;&#x6574;&#x9519;&#x8BEF;&#x4EE3;&#x7801;&#x8BF7;&#x89C1;&#x5B8C;&#x6574;&#x9519;&#x8BEF;&#x4EE3;&#x7801;&#x9875;<b>&#x3002;</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onQRCodeLoad &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x4E8C;&#x7EF4;&#x7801;&#x9996;&#x6B21;&#x6210;&#x529F;&#x52A0;&#x8F7D;&#x65F6;&#x56DE;&#x8C03;&#x3002;&#x56DE;&#x8C03;&#x53C2;&#x6570;
        qrcode &#x662F;&#x4E00;&#x4E2A;&#x5B57;&#x5178;&#xFF0C;&#x5305;&#x542B;&#x4E24;&#x4E2A;&#x5B57;&#x6BB5;&#xFF1A;qrcodeId&#x3001;qrcodeUrl&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">onQRCodeShow &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x4E8C;&#x7EF4;&#x7801;&#x9996;&#x6B21;&#x51FA;&#x73B0;&#x5728;&#x9875;&#x9762;&#x4E0A;&#x65F6;&#x56DE;&#x8C03;&#x3002;&#x56DE;&#x8C03;&#x53C2;&#x6570;
        qrcode &#x540C;&#x4E0A;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">onQRCodeLoadFaild &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x4E8C;&#x7EF4;&#x7801;&#x52A0;&#x8F7D;&#x5931;&#x8D25;&#x65F6;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#x3002;</td>
    </tr>
  </tbody>
</table>下面是一个最简的调用：一共只需要 5 行代码。

```javascript
authing.startAppAuthScanning({
  onSuccess(userInfo) {
    localStorage.setItem('token', userInfo.token);
  }
})
```

扫码组件示例：

![](../../.gitbook/assets/image%20%28455%29.png)

## 生成二维码

Authing.geneQRCode\(options\)

```javascript
authing.geneQRCode({ 
    scene: "APP_AUTH", 
    userDefinedData: {
        customVar1: "xxx", 
        customVar2: "xxx"   
    }
})
```

| 参数 | 可选/必选 | 说明 |
| :--- | :--- | :--- |
| scene | 必选 | 场景值。为常量值，填 APP\_AUTH。 |
| userDefinedData | 可选 | 自定义数据。类型为任意对象。 |

请求示例：

```javascript
const res = await authing.geneQRCode({ 
    scene: "APP_AUTH", 
    userDefinedData: {
        customVar1: "xxx", 
        customVar2: "xxx"   
    }
})
if(res.code === 200){
    const { qrcodeId, qrcodeUrl } = res.data
}
```

返回结果：

```javascript
{
    "code": 200, // 200 表示正常
    "message": "生成二维码成功",
    "data": {
        "qrcodeId": "5e061366445c9985e5bf890a", // 二维码 ID
        "qrcodeUrl": "https://usercontents.authing.co/qrcode/5e061366445c9985e5bf890a.png" // 二维码链接
    }
}
```

使用[在线二维码解码工具](https://cli.im/deqr) 查看二维码数据如下：其中 customVar1 和 customVar2 保存到了 userDefinedData 对象中。

```text
{ 
   "scene":"APP_AUTH",
   "qrcodeId":"5e06c47da6b47a9433f8b3be",
   "userPoolId":"59f86b4832eb28071bdd9214",
   "createdAt":"2019-12-28T02:57:01.697Z",
   "expireAt":"2019-12-28T02:59:01.697Z",
   "userDefinedData":{ 
      "customVar1":"xxx",
      "customVar2":"xxx"
   }
}
```

## 查询二维码状态

Authing.checkQRCodeStatus\(options\)

```javascript
const res = await authing.checkQRCodeStatus({
    qrcodeId: "xxxxx",
    scene: "APP_AUTH"
})
```

| 参数 | 可选/必选 | 说明 |
| :--- | :--- | :--- |
| qrcodeId | 必选 | 二维码 ID |
| scene | 必选 | 场景值。为常量值，填 APP\_AUTH。 |

请求示例：

```javascript
authing.checkQRCodeStatus({
    qrcodeId: "xxxxx",
    scene: "APP_AUTH"
}).then(res => {
    console.log(res)
})
```

返回结果示例：

```javascript
{
  "code": 200, // 业务状态码，200 表示正常
  "message": "查询二维码状态成功",
  "data": {
    "qrcodeId": "5e061489445c9985e5bf890d",
    "scanned": false,
    "expired": false,
    "success": false,
    "canceled": false,
    "status": 0,
    "userInfo": {},
    "ticket": "", // 该字段不一定会有
    "description": "二维码还没有被扫描"
  }
}
```

请求结果字段说明：

* scanned: 二维码是否已经被扫描
* expired：二维码是否已经过期
* success：用户是否成功授权
* canceled：用户是否取消授权
* status
  * 0: 未知状态，即还未扫码，或者已经扫码但用户还没有点击同意授权或者取消授权。
  * 1: 用户同意授权
  * -1: 用户取消授权
* userInfo:
  * 默认情况下，在用户扫码之后，会包含昵称（nickname）和头像（photo）两个字段
  * 开发者也可以配置返回完整用户信息（包括登录凭证 token）
* ticket：用于换取完整用户资料。**此字段只有在用户同意授权之后才会出现。**详情见下文。

## 轮询二维码状态

Authing.startPollingQRCodeStatus\(options\)

> 此接口为 checkQRCodeStatus 的封装。

```javascript
authing.startPollingQRCodeStatus({
  qrcodeId,
  : 'APP_AUTH',
  interval: 1000,
  onPollingStart: function (intervalNum) {
    console.log("Start polling for qrcode status: ", intervalNum)
    // clearInterval(intervalNum)
  },
  onResult: function (res) {
    console.log("Got qrcode latest result: ", res)
  },
  onScanned: function (userInfo) {
    console.log("User scanned qrcode: ", userInfo)
  },
  onSuccess: function (data) {
    const { ticket, userInfo } = data;
    console.log(`User confirmed authorization: ticket = ${ticket}`, userInfo)
    // 获取用户信息
    authing.exchangeUserInfoWithTicket(ticket).then(res => {
      const { code } = res
      if (code === 200) {
        console.log("Exchange userInfo success: ", res)
      } else {
        console.log("Exchange userInfo failed: ", res)
      }
    })
  },
  onCancel: function () {
    console.log("User canceled authorization")
  },
  onExpired: function () {
    console.log("QRCode has expired.")
  },
  onError: function (data) {
    console.log("Chcek qrcode status failed: ", data)
  }
})

```

参数说明：



<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;</th>
      <th style="text-align:left">&#x53EF;&#x9009;/&#x5FC5;&#x9009;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">onPollingStart &lt;function&gt;</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">
        <p>&#x8F6E;&#x8BE2;&#x5F00;&#x59CB;&#x65F6;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x4F1A;&#x56DE;&#x8C03;&#x4E00;&#x6B21;</b>&#x3002;&#x56DE;&#x8C03;&#x53C2;&#x6570;
          intervalNum &#x4E3A; setInterval &#x8FD4;&#x56DE;&#x7684;&#x6570;&#x503C;&#xFF0C;&#x53EF;&#x4F7F;&#x7528;
          clearInterval &#x505C;&#x6B62;&#x8F6E;&#x8BE2;&#x3002;</p>
        <p>&#x5982;&#xFF1A;clearInterval(intervalNum)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onResult</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">
        <p>&#x6BCF;&#x6B21;&#x67E5;&#x8BE2;&#x83B7;&#x53D6;&#x5230;&#x6570;&#x636E;&#x90FD;&#x4F1A;&#x56DE;&#x8C03;&#xFF0C;&#x53C2;&#x6570;
          res &#x793A;&#x4F8B;&#x5982;&#x4E0B;&#xFF1A;</p>
        <p><code>{    &quot;code&quot;: 200,    &quot;message&quot;: &quot;&#x67E5;&#x8BE2;&#x4E8C;&#x7EF4;&#x7801;&#x72B6;&#x6001;&#x6210;&#x529F;&quot;,    &quot;data&quot;: {        &quot;qrcodeId&quot;: &quot;5e05f6027fde537d950f7da9&quot;,        &quot;scanned&quot;: false,        &quot;expired&quot;: false,        &quot;success&quot;: false,        &quot;canceled&quot;: false,        &quot;status&quot;: 0,        &quot;userInfo&quot;: {},        &quot;description&quot;: &quot;&#x4E8C;&#x7EF4;&#x7801;&#x8FD8;&#x6CA1;&#x6709;&#x88AB;&#x626B;&#x63CF;&quot;    }}</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onScanned</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x626B;&#x7801;&#x65F6;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x4F1A;&#x56DE;&#x8C03;&#x4E00;&#x6B21;</b>&#x3002;&#x56DE;&#x8C03;&#x53C2;&#x6570;
        userInfo &#x53EA;&#x5305;&#x542B;&#x4E86;&#x7528;&#x6237;&#x6635;&#x79F0;&#x548C;&#x5934;&#x50CF;&#xFF0C;&#x5F00;&#x53D1;&#x8005;&#x53EF;&#x4EE5;&#x5C06;&#x5176;&#x5C55;&#x793A;&#x5728;&#x626B;&#x7801;&#x6846;&#x4E2D;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">onSuccess</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x540C;&#x610F;&#x6388;&#x6743;&#x4E4B;&#x540E;&#x5C06;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x4F1A;&#x56DE;&#x8C03;&#x4E00;&#x6B21;</b>&#xFF0C;<b>&#x4E4B;&#x540E;&#x8F6E;&#x8BE2;&#x7ED3;&#x675F;</b>&#x3002;&#x53C2;&#x6570;
        data &#x662F;&#x4E00;&#x4E2A;&#x5B57;&#x5178;&#xFF0C;&#x5305;&#x542B;&#x4E24;&#x4E2A;&#x5B57;&#x6BB5;&#xFF1A;ticket
        &#x548C; userInfo&#x3002;&#x51FA;&#x4E8E;&#x5B89;&#x5168;&#x6027;&#x8003;&#x8651;&#xFF0C;&#x9ED8;&#x8BA4;&#x60C5;&#x51B5;&#x4E0B;&#xFF0C;userInfo
        &#x53EA;&#x4F1A;&#x5305;&#x542B;&#x6635;&#x79F0;&#xFF08;nickname&#xFF09;&#x548C;&#x5934;&#x50CF;&#xFF08;photo&#xFF09;&#x4E24;&#x4E2A;&#x5B57;&#x6BB5;&#xFF0C;&#x5F00;&#x53D1;&#x8005;&#x4E5F;&#x53EF;&#x4EE5;&#x5728;&#x540E;&#x53F0;&#x914D;&#x7F6E;&#x4F7F;&#x5176;&#x8FD4;&#x56DE;&#x5B8C;&#x6574;&#x7528;&#x6237;&#x4FE1;&#x606F;&#xFF0C;&#x8BE6;&#x60C5;&#x89C1;&#x81EA;&#x5B9A;&#x4E49;&#x914D;&#x7F6E;&#x3002;
        ticket &#x53EF;&#x4EE5;&#x7528;&#x6765;&#x6362;&#x53D6;&#x5B8C;&#x6574;&#x7684;&#x7528;&#x6237;&#x4FE1;&#x606F;&#xFF0C;&#x76F8;&#x5173;&#x63A5;&#x53E3;&#x89C1;&#x4E0B;&#x6587;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">onCancel</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x53D6;&#x6D88;&#x6388;&#x6743;&#x53EA;&#x4F1A;&#x4F1A;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x56DE;&#x8C03;&#x4E00;&#x6B21;&#xFF0C;&#x4E4B;&#x540E;&#x8F6E;&#x8BE2;&#x7ED3;&#x675F;&#x3002;</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onExpired</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x4E8C;&#x7EF4;&#x7801;&#x5931;&#x6548;&#x65F6;&#x88AB;&#x56DE;&#x8C03;&#xFF0C;<b>&#x53EA;&#x56DE;&#x8C03;&#x4E00;&#x6B21;&#xFF0C;&#x4E4B;&#x540E;&#x8F6E;&#x8BE2;&#x7ED3;&#x675F;&#x3002;</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">onError</td>
      <td style="text-align:left">&#x53EF;&#x9009;</td>
      <td style="text-align:left">&#x6BCF;&#x6B21;&#x67E5;&#x8BE2;&#x5931;&#x8D25;&#x65F6;&#x90FD;&#x4F1A;&#x56DE;&#x8C03;&#x3002;&#x56DE;&#x8C03;&#x53C2;&#x6570;
        data &#x793A;&#x4F8B;&#x5982; {&quot;code&quot;: 2241,&quot;message&quot;:
        &quot;&#x4E8C;&#x7EF4;&#x7801;&#x4E0D;&#x5B58;&#x5728;&quot;,&quot;data&quot;:
        null}<b>&#x3002;</b>&#x5B8C;&#x6574;&#x9519;&#x8BEF;&#x4EE3;&#x7801;&#x8BF7;&#x89C1;&#x5B8C;&#x6574;&#x9519;&#x8BEF;&#x4EE3;&#x7801;&#x9875;<b>&#x3002;</b>
      </td>
    </tr>
  </tbody>
</table>![&#x4E00;&#x4E2A;&#x5B8C;&#x6574;&#x7684;&#x626B;&#x7801;&#x6D41;&#x7A0B;](../../.gitbook/assets/image%20%28304%29.png)

## ticket 换用户信息接口

Authing.exchangeUserInfoWithTicket\(ticket\)

```javascript
const res = authing.exchangeUserInfoWithTicket(ticket)
```

参数说明：

| 参数 | 可选/必选 | 说明 |
| :--- | :--- | :--- |
| ticket | 必选 | 从二维码状态查询接口得到的 ticket |

调用示例：

```javascript
authing.exchangeUserInfoWithTicket(ticket).then(res => {
  const { code } = res
  if (code === 200) {
    console.log("Exchange userInfo success: ", res.data)
  } else {
    console.log("Exchange userInfo failed: ", res.data)
  }
})
```

返回结果：

```javascript
{ 
   "code":200,
   "message":"换取用户信息成功",
   "data":{ 
      "_id":"5e05bbf2d51b3761d5c71070",
      "email":"983132@qq.com",
      "emailVerified":false,
      "oauth":"",
      "registerMethod":"default:username-password",
      "username":"983132@qq.com",
      "nickname":"",
      "company":"",
      "photo":"https://usercontents.authing.co/authing-avatar.png",
      "token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7ImVtYWlsIjoiOTgzMTMyQHFxLmNvbSIsImlxxxxxxxxx",
      "phone":"",
      "tokenExpiredAt":"2020-01-11T08:08:18.000Z",
      "loginsCount":1,
      "lastIP":"::1",
      "signedUp":"2019-12-27T08:08:18.115Z",
      "blocked":false,
      "isDeleted":false
   }
}
```

{% hint style="info" %}
注意：默认情况下，此接口**只允许在服务器端调用**，即需要使用用户池密钥初始化之后。

ticket 默认有效时间为 300 s。

开发者可在 [Authing 控制台](https://authing.cn/dashboard) **基础配置** -&gt; **基础设置** -&gt; **App 扫码登录 Web 自定义配置** 处修改。**详情见自定义配置项页。**
{% endhint %}



