---
description: 本文介绍几种常见社会化登录授权域参数含义。
---

# 社会化登录授权 Scope

## Scope 简介

许多社会化登录都基于 OAuth 2.0 协议，其中 Scope 的含义是：需要**请求用户授予**的权限。例如，当我们希望用户授予我们他的 Github 仓库读写权限，我们在发起授权登录时，必须要在 `scope` 参数中加入 `repo` 字段。

在 [**Authing 控制台**](https://authing.cn/dashboard) &gt; **第三方登录，**选择一个社会化登录选项卡，勾选发起授权登录时**请求用户授予**的权限：



## Github

| Scope 参数 | 描述 |
| :--- | :--- |
| **`(no scope)`** | 授予对**公开信息**的**只读访问权限**（包括公共用户配置文件信息，公共仓库信息和 gist） |
| **`repo`** | 授予对**私有和公有仓库**的**完全访问权限**。 这包括对代码，提交状态，仓库和组织项目，邀请，协作者，添加团队成员身份，部署状态的**读 / 写访问权限**，以及公开和私有仓库和组织仓库的 Webhook **读 / 写访问权限**。 还授予管理用户项目的能力。 |
|  `repo:status` | 授予对**公开和私有仓库提交状态**的**读 / 写访问权限**。 仅在授予其他用户或服务对**私有仓库提交状态**的访问权限，而**无需授予对代码的访问权限时**，此作用域才是必需的。 |
|  `repo_deployment` | 授予访问**公开和私有仓库**的**部署状态的权限**。 仅在授予其他用户或服务对**部署状态的访问权限**，而**不授予对代码的访问权限时**，才需要此作用域。 |
|  `public_repo` | 仅限于对**公开仓库的访问**。 这包括对代码，提交状态，仓库项目，协作者以及公开仓库和组织的部署状态的**读 / 写访问**。 对公开仓库加注星标也需要此 scope。 |
|  `repo:invite` | 授予**接受 / 拒绝邀请**从而在仓库上进行协作的能力。 仅在授予其他用户或服务邀请权限，而不授予对代码的访问权限时，才需要此作用域。 |
| **`admin:repo_hook`** | 授予对**公开和私有仓库**中的**仓库 hook 的读取，写入，ping 和删除访问权限**。 repo 和 public\_repo 授权域会授予对仓库的完全访问权限，包括仓库 hook。 使用 admin:repo\_hook 授权域将授权限制为仓库 hook。 |
|  `write:repo_hook` | 授予对公开或私有仓库中的 hook 的读取，**写入**和 ping 的访问权限。 |
|  `read:repo_hook` | 授予对公开或私有仓库中的 hook 的**读取和 ping** 访问权限。 |
| **`admin:org`** | **全部管理组织**及其团队，项目和成员的权限。 |
|  `write:org` | 对组织成员、组织项目和团队成员身份的**读写访问权限**。 |
|  `read:org` | 对组织成员、组织项目和团队成员身份的**只读访问权限**。 |
| **`admin:public_key`** | **全部管理公钥**的权限 |
|  `write:public_key` | 新建、查看公钥列表、详情的权限 |
|  `read:public_key` | 查看公钥列表、详情的权限 |
| **`admin:org_hook`** | 授予对**组织 hook 的读取，写入，ping 和删除访问权限**。 注意：OAuth 令牌仅能对那些由 OAuth 应用创建的组织 hook 执行操作。 个人访问令牌只能在用户自己创建的组织 hook 上执行这些操作。 |
| **`gist`** | 授予对 gist 的写权限 |
| **`notifications`** | 授予： \* 用户通知的读权限 \* 将 threads 标记为已读的权限 \* 对仓库进行 watch 和 unwatch 的权限 \* 读 / 写和删除 thread 订阅的权限 |
| **`user`** | 授予对**用户资料的读写权限**。注意此 scope 包含了 `user:email` 和 `user:follow`。 |
|  `read:user` | 授予对用户资料的读取权限 |
|  `user:email` | 授予对用户 email 地址的读取权限 |
|  `user:follow` | 授予对用户 unfollow 其他用户的权限 |
| **`delete_repo`** | 授予**删除用户可管理的仓库**的权限 |
| **`write:discussion`** | 授予**读 / 写团队讨论**的权限 |
|  `read:discussion` | 授予团队讨论的只读权限 |
| **`write:packages`** | 授予访问权限以**在 GitHub Packages 中上传或发布软件包**。 有关更多信息，请参见 [GitHub 帮助文档](https://help.github.com/github/managing-packages-with-github-packages/publishing-a-package)中的“发布软件包”。 |
| **`read:packages`** | 授予访问权限以**从 GitHub Packages 中下载或安装软件包**。 有关更多信息，请参阅 [GitHub 帮助文档](https://help.github.com/github/managing-packages-with-github-packages/installing-a-package)中的“安装软件包”。 |
| **`delete:packages`** | 授予访问权限以**从 GitHub Packages 中删除软件包**。 有关更多信息，请参阅 [GitHub 帮助文档](https://help.github.com/github/managing-packages-with-github-packages/deleting-a-package)中的“删除软件包”。 |
| **`admin:gpg_key`** | **管理 GPG 密钥**的全部权限 |
|  `write:gpg_key` | 新建、查看 GPG 密钥列表、详情的权限 |
|  `read:gpg_key` | 查看 GPG 密钥列表、详情的权限 |
| **`workflow`** | 授予**添加和更新 GitHub Actions 工作流文件**的能力。 如果同一仓库中的另一个分支上存在相同的文件（具有相同的路径和内容），则可以在没有此作用域的情况下提交工作流文件。 |

## 微信移动端网页 <a id="wechat-web-browser"></a>

| Scope 名称 | 描述 |
| :--- | :--- |
| **`snsapi_base`** | 授予通过 code 换取 access\_token、refresh\_token 和已授权 scope 的能力 |
| **`snsapi_userinfo`** | 获取用户个人信息的权限 |
| **`snsapi_login`** | 仅用于 PC 端扫码登录 |

> 其中 **snsapi\_base** 属于基础接口，若应用已拥有其它 scope 权限，则默认拥有 snsapi\_base 的权限。使用 snsapi\_base 可以让移动端网页授权绕过跳转授权登录页请求用户授权的动作，直接跳转第三方网页带上授权临时票据（code），但会使得用户已授权作用域（scope）仅为snsapi\_base，从而导致无法获取到需要用户授权才允许获得的数据和基础功能。

使用 **snsapi\_base** 会跳过用户的确权点击，后续使用 code 换取 access\_token，用 access\_token **也能够换取到用户信息**。

## 新浪微博 <a id="sina-blog"></a>

| Scope 参数 | 描述 |
| :--- | :--- |
| all | 请求下列所有 scope 权限 |
| email | 用户的联系邮箱，[接口文档](http://open.weibo.com/wiki/2/account/profile/email) |
| direct\_messages\_write | 私信发送接口，[接口文档](http://open.weibo.com/wiki/C/2/direct_messages/send) |
| direct\_messages\_read | 私信读取接口，[接口文档](http://open.weibo.com/wiki/C/2/direct_messages) |
| invitation\_write | 邀请发送接口，[接口文档](http://open.weibo.com/wiki/Messages#.E5.A5.BD.E5.8F.8B.E9.82.80.E8.AF.B7) |
| friendships\_groups\_read | 好友分组读取接口组，[接口文档](http://open.weibo.com/wiki/API%E6%96%87%E6%A1%A3_V2#.E5.A5.BD.E5.8F.8B.E5.88.86.E7.BB.84) |
| friendships\_groups\_write | 好友分组写入接口组，[接口文档](http://open.weibo.com/wiki/API%E6%96%87%E6%A1%A3_V2#.E5.A5.BD.E5.8F.8B.E5.88.86.E7.BB.84) |
| statuses\_to\_me\_read | 定向微博读取接口组，[接口文档](http://open.weibo.com/wiki/API%E6%96%87%E6%A1%A3_V2#.E5.BE.AE.E5.8D.9A) |
| follow\_app\_official\_microblog | 关注应用官方微博，该参数不对应具体接口，只需在应用控制台填写官方帐号即可。填写的路径：我的应用-选择自己的应用-应用信息-基本信息-官方运营账号（默认值是应用开发者帐号） |

## QQ

| Scope 参数 | 描述 |
| :--- | :--- |
| get\_user\_info | 获取登录用户的昵称、头像、性别，[接口文档](http://wiki.connect.qq.com/get_user_info) |
| list\_photo | 获取用户QQ空间相册中的照片列表，[接口文档](http://wiki.connect.qq.com/list_photo) |
| add\_album | 在用户的空间相册里，创建一个新的个人相册，[接口文档](http://wiki.connect.qq.com/add_album) |
| upload\_pic | 上传一张照片到QQ空间相册，[接口文档](http://wiki.connect.qq.com/upload_pic) |
| list\_album | 获取用户QQ空间相册列表，[接口文档](http://wiki.connect.qq.com/list_album) |
| get\_vip\_rich\_info | 获取QQ会员的高级信息，[接口文档](http://wiki.connect.qq.com/get_vip_rich_info) |
| get\_vip\_info | 获取QQ会员的基本信息，[接口文档](http://wiki.connect.qq.com/get_vip_info) |

## 接下来你可能需要：

刷新 Github、微信等三方的 token：

{% page-ref page="../../sdk/sdk-for-node/" %}

