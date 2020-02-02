# Authorization at Authing

Authing 支持非常丰富的认证、授权手段：

* 认证手段有：传统密码、验证码登录、丰富的第三方登录（微信、小程序、微博、GitHub、支付宝、QQ 等，未来还会有更多），以及企业级的 LDAP、SAML、OIDC 等。
* 授权手段有：完整的 OAuth、OIDC 流程。

  
对于授权流程，访问控制（Access Control）策略是非常重要的一环，目前 Authing 一共支持（或即将支持）三种访问控制手段：

* 老版本的用户角色（deprecated）
* RBAC
* ABAC（未来即将支持）

下面是 Authing  目前所支持的相关 Feature：

* YES ：当前支持。
* In future release ：已加入未来规划，不久后将会支持。
* To be determined ：还在设计是否需要添加此功能。

| Feature | 备注 | Authing 支持情况 |
| :--- | :--- | :--- |
| 角色 |  |  |
| 创建/修改/删除 角色 |  | In future release |
| 分页查询 |  | YES |
| 通过名称、描述搜索角色 |  | YES |
| 角色能被授予给分组 |  | YES |
| 角色嵌套、分层 |  | In future release |
| 角色通过 namespace、多租户管理 |  | In future release |
| 查询角色具备的所有权限 |  | YES |
| 查询角色中包含的所有用户 |  | YES |
|  |  |  |
| 用户 |  |  |
| 创建/修改/删除 用户 |  | YES |
| 分页查询 |  | YES |
| 通过昵称、邮箱搜索用户 |  | YES |
| 查看最近注册、登录的用户 |  | YES |
| 通过第三方应用查找用户 |  | In future release |
| 通过 lucence 语法搜索用户 |  | In future release |
| 用户可以拥有一个或多个角色 | 最多50个 | YES |
| 用户能在一个或多个分组里 | 最多50个 | YES |
| 查看一个用户具备的所有角色 |  | YES |
| 查看一个用户所在的所有分组 |  | YES |
| 查看一个用户所具备的所有权限 |  | YES |
| 通过 JSON 导入/导出用户 |  | YES |
| 自定义密码加密函数 |  | YES |
|  |  |  |
| 权限 |  |  |
| 创建/修改/删除 权限 |  | YES |
| 分页查询 |  | YES |
| 通过名称、描述搜索权限 |  | In future release |
| 能直接赋予用户权限 |  | To be determined |
| 能授权给一个或多个角色 |  | YES |
| 查询所有具有某个权限的用户 |  | In future release |
| 查询所有具有某个权限的角色 |  | In future release |
| 查询所有具有某个权限的分组 |  | In future release |
|  |  |  |
| 分组 |  |  |
| 分页查询 |  | YES |
| 创建/修改/删除 分组 |  | YES |
| 通过名称、描述搜索分组 |  | In future release |
| 直接从第三方用户目录导入（如 AD, LDAP, SAML） |  | In future release |
| 分组嵌套、分层 |  | In future release |
| 查看分组的子分组 |  | In future release |
| 分组通过 namespace、多租户管理 |  | In future release |
| 查看一个分组具备的所有用户 |  | YES |
| 查看一个分组具备的所有角色 |  | YES |
| 查看一个分组具备的所有权限 |  | YES |
|  |  |  |
| 配置 |  |  |
| 自定义授权流程策略（authorization policies） |  | In future release |
| 自定义是否将权限加入 Token | 默认为是 | In future release |
| 自定义是否将角色加入 Token | 默认为是 | In future release |
| 自定义是否将分组加入 Token | 默认为是 | In future release |



