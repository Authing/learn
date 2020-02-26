---
description: 本文讲述如何使用 Authing 控制台调试 Pipeline 函数。
---

# 如何调试

我们已经[创建了第一个 Pipeline 函数](write-your-first-pipeline-function.md)，同时回顾一下该函数代码为：

```javascript
async function pipe(context, callback) {
  const email = context.data.userInfo.email;
  // 非邮箱注册方式
  if (!email) {
    return callback(null, context)
  }
  if (!email.endsWith("example.com")) {
    return callback(new Error('Access denied.'));
  }
  return callback(null, context);
}
```

该 Pipeline 函数的作用是只允许域名后缀为**`example.com`**的邮箱注册。

进入该函数详情页，最下方有一个“调试代码“ 按钮：

![](../../.gitbook/assets/image%20%2864%29.png)

点击此按钮打开调试窗口：Authing 会**根据你的用户池**生产相对应的测试数据。

![](../../.gitbook/assets/image%20%28394%29.png)

**注意到测试数据中 `data.userInfo.email` 为  `xxxxxx@example.com`。**

点击 “调试“ 按钮：下方输出了调试结果，我们得到了最新的 context 对象，说明此邮箱通过了该 Pipeline 函数。

![](../../.gitbook/assets/image%20%2869%29.png)

我们再将 email 后缀改为 qq.com，输出结果区域我们得到了相应的错误提示。

![](../../.gitbook/assets/image%20%28189%29.png)

## 查看 log 日志 \(BETA\) <a id="log"></a>

{% hint style="warning" %}
需要使用  Authing Pipeline 全局内置函数 `log` 才能查看到运行日志，不是 `console.log` !
{% endhint %}

使用函数编辑器内修改代码，在函数最前面加入一行 : 注意是 log 而非 console.log 。

```javascript
log(context)
```

![](../../.gitbook/assets/image%20%2863%29.png)

再次点击“调试“，就能看到输出的日志了。

![](../../.gitbook/assets/image%20%2826%29.png)

{% hint style="info" %}
如果没有日志输出，请重试几次！
{% endhint %}

## 捕捉语法错误 <a id="syntax-error"></a>

如果你的函数有语法错误， Authing Pipeline 也能捕捉到，这里我们修改一下代码，在 pipe 函数最前面加上了一行 xxxxxxx：

![](../../.gitbook/assets/image%20%28474%29.png)

再次点击调试，可以看到相应的错误提示：`message: “xxxxxxx is not defined”`。

![](../../.gitbook/assets/image%20%28172%29.png)

{% hint style="info" %}
我们推荐先用调试器调试代码通过之后再将此函数发不到线上。
{% endhint %}



