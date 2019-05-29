# 同时使用 Authing 作为 SP 和 IdP

本教程讲述如何使用 Authing 云上的 SAML SP 和 SAML IdP 为企业或个人快速接入单点认证能力。完成本教程后，你会拥有运行在 Authing 云上的 SAML Service Provider 和 SAML Identity Provider 来处理 SAML 协议相关的过程，而你只需关注 Authing 返回的用户信息与 JWT Token。

在用户访问受保护的资源时，结合 Authing SDK 可以对他携带的 JWT Token 进行验证。如果 JWT Token 验证失败，只需将用户重定向到 SAML Service Provider 的云上地址，它会负责通过 SAML 协议与 IdP 交换信息从而认证用户，并返回 JSON 格式的用户信息。

## 创建一个 SAML IdP 应用

进入应用控制台 -&gt; 第三方登录 -&gt; SAML IdP 选项卡，点击「创建 SAML IdP」

填写应用信息，为云上的 IdP 指定一个二级域名，点击「确定」

此时我们还没有 SP，所以 AssertionConsumeServiceURL 可以先填一半，省略 appId 以后的部分。

其他配置使用默认即可。

创建完成后，转到使用方法页。记录 IdP 登录 URL、IdP 登出 URL 和 IdP EntityID，后续创建 SP 时会使用。

## 创建一个 SAML SP 应用

进入应用控制台 -&gt; 第三方登录 -&gt; 社会化登录，在 SAML Service Provider 区域，点击「+」

填写应用信息，为云上的 SP 指定一个二级域名，点击「确定」 

在 IdP 登录 URL、IdP 登出 URL、IdP EntityID 输入刚才记录的三项内容。

其他配置使用默认即可。点击「确定」

在 IdP 字段映射方案列表中选择 Authing，点击「确定」

创建完成后，转到使用方法页。记录 AssertionConsumeServiceURL、下载 SP Metadata。

## 编辑刚创建的 IdP 应用

进入应用控制台 -&gt; 第三方登录 -&gt; SAML IdP 选项卡，找到刚刚创建的 IdP，点击右侧的「...」详情按钮，进入编辑。

在基础配置选项卡，填入刚才记录的 AssertionConsumeServiceURL、上传刚才下载的 SP Metadata，点击「确定」

在使用方法选项卡，下载 IdP Metadata。

## 编辑刚创建的 SP 应用

进入应用控制台 -&gt; 第三方登录 -&gt; 社会化登录，在 SAML Service Provider 区域，找到刚才创建的 SP 应用，点击「编辑」

在基础配置选项卡，上传刚才下载的 SP Metadata。点击「确定」

## 开始使用

当你的受保护资源被访问时，需要先对用户身份进行认证，可以通过 Authing SDK 检验 JWT Token 的合法性。如果 token 不合法，可以将用户重定向到云上的 SAML SP，完成后续的用户身份认证流程，Authing 云上的 SAML SP 最终会携带着用户信息和 JWT Token 跳回在 SP 中设置的回调地址。

云上的 SAML SP 地址形式如下

让 SP 通过 GET 请求发送 SAML Request

```text
GET https://<domain>.authing.cn/oauth/saml/sp/<appId>/spinitsso-redirect
```

让 SP 通过 POST 请求发送 SAML Request

```text
GET https://<domain>.authing.cn/oauth/saml/sp/<appId>/spinitsso-post
```

{% hint style="info" %}
以下是一次完整的 SAML 登录流程
{% endhint %}

### 访问云上的 SP



### 跳转到 IdP 认证页



### 用户进行登录



### Authing 将用户信息发到回调地址






