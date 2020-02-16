# 自定义 Guard 的 UI 样式

如果你阅读过[实现单点登录](../../quickstart/implement-sso-with-authing.md)那么你就会了解 Authing 支持自定义二级域名，终端用户可以访问该二级域名完成身份认证。

比如， 我们有一个用来演示云上 SSO 的 URL 示例：[sample-sso.authing.cn](https://sample-sso.authing.cn)。访问此链接后可以看到如下界面：

![](../../.gitbook/assets/image%20%28189%29.png)

此界面的所有样式都可根据使用者的需求进行修改，修改方式如下（假设你[创建了 OIDC 应用](../../advanced/oidc/create-oidc.md)）：

依次点击**第三方登录**  -&gt; **OIDC 应用** -&gt; **选择你创建好的应用**

![](../../.gitbook/assets/image%20%28386%29.png)

点击**「编辑 CSS 代码」**后会弹出来一个用来编辑 CSS 代码的编辑器：

![](../../.gitbook/assets/image%20%28194%29.png)

在此编辑器中编辑 Guard 的样式，保存后可即时预览。

{% hint style="info" %}
出于安全考虑，目前尚不支持自定义 JavaScript 代码。
{% endhint %}

