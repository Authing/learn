---
description: 了解 OIDC 包含的常见问题，在开发时可以查阅此文档。
---

# OIDC 常见问题

## OIDC 在后端如何处理

请参考 Github 上的示例：[oidc-demo](https://github.com/Authing/oidc-demo)

## OIDC 三种认证流程的特征对比

| 特性 | 授权码流程 | 隐式流程 | 混合流程 |
| :--- | :--- | :--- | :--- |
| 所有 token 全部从授权路由返回 | no | yes | no |
| 所有 token 都从 token 路由返回 | yes | no | no |
| token 不会暴露给前端 | yes | no | no |
| 客户端可以被 AP 认证 | yes | no | yes |
| 可以刷新 token | yes | no | yes |
| 一次交互 | no | yes | no |
| 必须服务器-服务器通信 | yes | no | varies |

## 不同 response\_type 对应的授权流程

| "response\_type" value | Flow |
| :--- | :--- |
| code | Authorization Code Flow（授权码流程） |
| id\_token | Implicit Flow（隐式流程） |
| id\_token token | Implicit Flow（隐式流程） |
| code id\_token | Hybrid Flow（混合流程） |
| code token | Hybrid Flow（混合流程） |
| code id\_token token | Hybrid Flow（混合流程） |

[参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#Authentication)

## 如何验证 Token 合法性

请参考：[使用 OIDC 应用的密钥验证 Token 合法性](https://docs.authing.cn/authing/advanced/authentication/verify-jwt-token#oidc-secret-token)。

## scope 参数对应的用户信息 <a id="scope-params-detail"></a>

| scope 名称 | 对应信息 |
| :--- | :--- |
| address | address |
| email | email，email\_verified |
| phone | phone\_number, phone\_number\_verified |
| profile | birthdate，family\_name，gender，given\_name，locale，middle\_name，name，nickname，picture，preferred\_username，profile，updated\_at，website，zoneinfo |
| offline\_access | 如果存在此参数，token 接口会返回 refresh\_token 字段 |
| unionid | unionid |
| authing\_token | 用户信息中的 Authing Token |

## 用户信息字段含义

| 字段名 | 翻译 |
| :--- | :--- |
| sub | 唯一标识 |
| openid | openid |
| unionid | unionid |
| name | 姓名 |
| given\_name | 名字 |
| family\_name | 姓氏 |
| middle\_name | 中间名 |
| nickname | 昵称 |
| preferred\_username | 希望被称呼的名字 |
| profile | 基础资料 |
| picture | 头像 |
| website | 网站链接 |
| email | 电子邮箱 |
| email\_verified | 认证邮箱 |
| gender | 性别 |
| birthdate | 生日 |
| zoneinfo | 时区 |
| locale | 区域 |
| phone\_number | 手机号 |
| phone\_number\_verified | 认证手机号 |
| address | 地址 |
| formatted | 详细地址 |
| street\_address | 街道地址 |
| locality | 城市 |
| region | 省 |
| postal\_code | 邮编 |
| country | 国家 |
| updated\_at | 信息更新时间 |

[参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims)  


## id\_token、access\_token 和 token 之间的区别

当你需要向你**自己的服务器**请求资源时，应该携带 **id\_token**，同时你的服务器应该[检验此 token 的合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token#oidc-secret-token)，然后再返回相应资源。**id\_token** 相当于终端用户的身份证，用于认证用户身份。

当你需要向 **Authing 服务器**请求属于该用户的资源时，需要携带 **access\_token**，拥有 **access\_token**，你就能够以终端用户的身份，访问位于 Authing 服务器上属于他的资源。

当你需要向 **Authing 服务器**请求属于该用户的资源时，也可以携带 **token**。终端用户完成登录后，你的应用前端通过 [AuthingSSO SDK](https://github.com/Authing/AuthingSSO) 中的 trackSession 函数能够直接获取到用户信息，其中的 **token** 字段，相当于 Authing 与该终端用户维持内部会话状态的 Cookie；使用 OIDC 协议授权时，如果发起授权请求时的 scope 包含 `authing_token` ，在用户信息接口获取到的用户信息中会包含 **token** 字段，相当于 Authing 与该终端用户维持内部会话状态的 Cookie。

