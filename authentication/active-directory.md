---
description: 本文介绍如何在 Authing 控制台配置你的 Active Directory 连接。
---

# 接入 Active Directory

## 准备工作

你一共需要准备以下内容：

1. [注册 Authing 开发者账号](../quickstart/create-authing-account.md)；
   * 在创建 Authing 开发者账号的过程中，你会[创建自己的第一个 OIDC 应用](oidc/create-oidc.md)。
2. 运行你的 Windows Active Directory 服务；
3. 安装 Authing AD Connector。

## 在 Authing 控制台配置 Active Directory 连接

进入 [Authing 控制台](https://authing.cn/dashboard)，按照下图指引找到 第**三方登录** &gt; **Active Directory** 页面：

![](https://cdn.authing.cn/blog/20200412234908.png)

点击「**创建 AD 连接**」按钮：

![](https://cdn.authing.cn/blog/20200412235059.png)

你需要输入**名称**，然后点击「**创建**」按钮：

![](https://cdn.authing.cn/blog/20200412235227.png)

在下一个页面，你需要选择为你的哪些 OIDC/OAuth 应用开启  AD 登录选项：

{% hint style="info" %}
有关如何接入 OIDC/ OAuth 2.0 的详情请见：

1. [接入 OAuth 2.0](oauth2/)
2. [接入 OpenID Connect](oidc/)
{% endhint %}

![](https://cdn.authing.cn/blog/20200412235347.png)

点击「**确定**」，进入下一个「**使用说明**」页面：

![](https://cdn.authing.cn/blog/20200412235816.png)

首先你需要[下载](https://download.authing.cn/app/Authing-AD-Connector-latest.exe) Authing AD Connector，这是一个 exe 文件，需要运行在你的 Windows AD 服务器，负责和 Authing 进行通信。同时你需要记录下刚刚创建的 AD 连接唯一标志（Connector ID）和连接密钥（Connector Secret），之后填入 Authing AD Connector 。

之后你就可以看到刚刚创建的 AD 连接了，你可以：

1. 查看 AD 连接在线状态；
2. 查看 AD 连接详情，包括连接 ID、连接密钥、开启了 AD 连接的 OAuth/OIDC 应用等；
3. 启用或停用 AD 连接，停用的 AD 连接不会显示在 Guard 登录表单中。

![](https://cdn.authing.cn/blog/20200413000650.png)

接下来你需要在 Windows 运行 Authing AD Connector 。

## 在 Windows 运行 Authing AD Connector

### 前提条件

* Windows 服务器；
* 服务器安装了 Active Directory；
* 运行 Authing AD Connector 的机器上，能够连通 Active Directory；
* 一个具有 Active Directory 的读取权限的用户账密。

Authing AD Connector 需要**安装在局域网 AD 域环境**中，但不一定要安装到运行 AD 服务的服务器上，只要保证 Authing AD Connector 能够访问到 AD 用户目录。在安装 Authing AD Connector 之前，请先确保满足以上条件。

### 安装 Authing AD Connector

点击[这里](https://download.authing.cn/app/Authing-AD-Connector-latest.exe)下载最新的 Authing AD Connector。

将下载的文件上传到 AD 域环境的机器上，双击应用，进行安装。

![](https://cdn.authing.cn/docs/20200414213654.png)



系统可能提出警告，点击「仍要运行」。

![](../.gitbook/assets/image%20%28521%29.png)



选择语言，点击「OK」。

![](https://cdn.authing.cn/docs/20200414213931.png)



点击「下一步」。

![](https://cdn.authing.cn/docs/20200414214254.png)



接受许可协议并点击「下一步」。

![](https://cdn.authing.cn/docs/20200414214406.png)



选择软件安装目录，然后点击「安装」。

![](https://cdn.authing.cn/docs/20200414214502.png)



等待安装完成。

![](https://cdn.authing.cn/docs/20200414214624.png)



点击「完成」，会弹出命令行窗口，等待安装完成。

![](https://cdn.authing.cn/docs/20200414214751.png)



中途可能会出现缺少可选依赖的报错信息，可以忽略。看到以下界面说明安装成功，可按任意键退出：

![](https://cdn.authing.cn/docs/20200414214912.png)



接下来，打开浏览器，访问 localhost:9742，会看到以下界面：

![](https://cdn.authing.cn/docs/20200416004727.png)

将你的 AD Connector ID、AD Connector Secret、AD 服务器链接地址、Base DN、域用户名、密码填入，然后点击「**保存**」按钮。

![](https://cdn.authing.cn/docs/20200414215448.png)



之后，点击「测试连接」，出现以下结果，说明连接正常：

![](https://cdn.authing.cn/docs/20200414220049.png)

{% hint style="info" %}
如果遇到 Connector 与 Authing 链接测试失败问题，请稍等一会儿，可能因为网络延迟问题，Connector 与 Authing 握手尚未完成。
{% endhint %}

{% hint style="info" %}
如果遇到 AD 相关的错误，请检查 AD 服务器链接、账密信息是否正确。
{% endhint %}

## 使用 Guard 体验 AD 登录

先按照上述步骤完成 AD Connector 的开启，然后访问你的 OIDC 或 OAuth 应用的登录地址。点击「使用 AD」单选框，切换到 AD 登录。

![](https://cdn.authing.cn/docs/20200416005523.png)



输入 AD 域用户账号和密码信息，点击「登录」。

![](https://cdn.authing.cn/docs/20200416005738.png)



之后会提示授权，点击「授权登录」后，跳转到业务地址。

![](https://cdn.authing.cn/docs/20200416010008.png)

{% hint style="danger" %}
Authing 不提供 AD 测试服务器，本示例 AD 域用户信息已经失效。
{% endhint %}

