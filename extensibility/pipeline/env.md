# 使用环境变量

{% hint style="success" %}
Pipeline 函数中的环境变量和操作系统里环境变量的概念一致，开发者可以通过全局变量 env 获取。环境变量对应用池内所有 Pipelien 函数全部可见。
{% endhint %}

[创建了你的第一个 Pipeline 函数](write-your-first-pipeline-function.md)之后，自定义 Pipeline 页面的最下方会出现管理环境变量的区域：

![](../../.gitbook/assets/image%20%28334%29.png)

环境变量为一组 Key-Value Pair 值，可以用于保存 WebHook 链接、密钥等数据。

输入 Key  和 Value  之后点击添加：

![](../../.gitbook/assets/image%20%28226%29.png)

在此，我们设置了一个 Key 为 `LARK_WEBHOOK` 的环境变量，在 Pipeline 函数中可以通过 `env.LARK_WEBHOOK` 获取对应的 Value 值。如下面这个用户注册之后触发飞书群通知的例子：

```javascript
async function pipe(user, context, callback) {
  const webhook = env.LARK_WEBHOOK
  await axios.post(webhook, {
    title: "New User Registered - From Authing Rules Pipeline",
    text: `
用户信息：
ID: ${user._id}
昵称：${user.username}
注册方式：${user.registerMethod}
邮箱：${user.email}
手机号：${user.phone}
UA: ${user.device}
用户池 ID: ${user.registerInClient}
`
  })
  return callback(null, user, context)
}
```

除了使用控制台，我们还提供了 Node SDK 管理 Pipeline 环境变量：

{% page-ref page="node-sdk.md" %}

非 JS 开发者请见：

{% page-ref page="graphql-api.md" %}



