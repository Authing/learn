# Guard VS 自定义 UI

当将 Authing 接入你的应用程序时，最佳解决方案是使用 Authing 的通用登录 UI Guard。 本文将帮助你选择什么时候该使用 Guard 以及什么时候需要自己开发 UI。

Guard 帮助开发者屏蔽了很多底层认证的实现细节，同时也包括繁琐的 UI 开发。

以下是 Guard 支持的平台：

{% page-ref page="guard.md" %}

## Guard 与自定义 UI 对比

Guard 是一个可嵌入任意应用程序的登录部件，它提供一组标准行为。

[Authing SDK](https://learn.authing.cn/authing/sdk/sdk) 是客户端库，没有用户界面，但开发者可以自己自定义登录页面的行为和外观。

下面简要概述了你什么时候需要使用 Guard，什么时候需要使用自定义 UI：

| **UI 功能：** | Guard | 自定义 UI |
| :--- | :--- | :--- |
| 拥有精美的设计，适合大多数现代网站，只需对其选项进行一些调整。 | Yes | No |
| 初始化时有详细的参数，可以控制整个表单的行为 | Yes | No |
| 假如你在控制台开启了社会化登录，Guard 会自动显示（扫码登录同理） | Yes | No |
| 记住上一次登录用户的信息 | Yes | No |
| 支持手机-密码、邮箱-密码、以及忘记密码、重置密码等繁琐且需要重复开发的功能 | Yes | No |
| 支持 LDAP 登录 | Yes | No |
| 由 Authing 保证安全的认证 | Yes | Yes |
| 贵司对 UI 有严格的设计要求和统一性要求 | No | Yes |
| 贵司希望能在现有 UI 上做改动 | No | Yes |
| 允许前端开发专家使用 HTML，CSS 和 JavaScript 进行自定义 | No | Yes |
| 处理多个数据库或 Active Directory 连接 | No | Yes |

## Guard

Guard 是一个登录表单，使用户可以使用选定的连接轻松进行身份验证。 Guard 将自动实现用户注册和登录所涉及的大部分细节。 

![](../.gitbook/assets/image%20%28189%29.png)

使用 Guard，你将实现以下 UI：

1. 功能强大，可在任何设备上提供出色的用户体验；
2. 有一个简单的设计适合大多数现代网站；
3. 可配置性，为每个不同的登录方式显示相应的表单控件，并能根据你在控制台的配置显示不同的社会化登录、LDAP 登录等；
4. 自动记住给定用户的上次的登录信息；

虽然你无法完全改变 Guard 的行为，但你可以配置几个基本选项以控制 Guard 的显示。

![](../.gitbook/assets/image%20%28305%29.png)

### 何时使用 Guard

如果你认可下面的观点，请考虑使用 Guard：

1. 你喜欢 Guard 的结构，外观和感觉；
2. 你喜欢更快速，更轻松地接入 Authing 和登录 UI；
3. 你的流程包含许多 Guard 处理开箱即用的功能： 
   1. 用户注册、登录和密码重置
   2. 使用手机 - 验证码登录
   3. 小程序扫码登录
   4. 使用社会化登录
   5. 使用 LDAP 登录

## 自定义 UI

如果 Guard 的标准化行为无法满足你应用程序的要求，或者你有复杂的自定义身份认证过程，则需要自定义用户界面。 

[使用 Authing 的 Web SDK](https://learn.authing.cn/authing/sdk/sdk/authing-sdk-for-web)，你可以自定义用户注册和登录的流程。 如果你愿意，也可以直接使用 [Authentication GraphQL](https://learn.authing.cn/authing/sdk/open-graphql)。

![](../.gitbook/assets/image%20%28111%29.png)

### 何时使用自定义 UI

在以下情况下，请考虑为你的应用自定义 UI：

1. 你对用户界面的外观有严格的要求；
2. 你对文件大小有严格的要求（Authing SDK 小于 Guard）；
3. 你熟悉 HTML，CSS 和 JavaScript - 你可以很熟练的实现自己的 UI；
4. 你只需要处理邮箱 - 密码和社会登录；
5. 你有多个数据库或 Active Directory 连接。



