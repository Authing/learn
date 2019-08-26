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
| code | Authorization Code Flow |
| id\_token | Implicit Flow |
| id\_token token | Implicit Flow |
| code id\_token | Hybrid Flow |
| code token | Hybrid Flow |
| code id\_token token | Hybrid Flow |

[参考 OIDC 规范](https://openid.net/specs/openid-connect-core-1_0.html#Authentication)

## 如何验证 Token 合法性

请参考：[使用 OIDC 应用的密钥验证 Token 合法性](https://learn.authing.cn/authing/advanced/authentication/verify-jwt-token#shi-yong-oauth-huo-oidc-ying-yong-de-mi-yao-yan-zheng-token)。

## scope 参数对应的用户信息

| scope 名称 | 对应信息 |
| :--- | :--- |
| address | address |
| email | email，email\_verified |
| phone | phone\_number, phone\_number\_verified |
| profile | birthdate，family\_name，gender，given\_name，locale，middle\_name，name，nickname，picture，preferred\_username，profile，updated\_at，website，zoneinfo |
| offline\_access | token 接口返回 refresh\_token 字段 |
| unionid | unionid |

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


## id\_token 和 access\_token 之间的区别

id\_token 用于鉴定用户身份，相当于用户的身份证，access\_token 用于向服务器获取更详细的用户信息（比如邮箱，手机，地区等）。

