# 接入 RBAC

## 学习 RBAC 的基本概念

{% page-ref page="authorization/rbac.md" %}

## SDK

{% page-ref page="../sdk/sdk-for-node/rbac.md" %}

{% page-ref page="../sdk/sdk-for-node/query-permissions.md" %}

## 如何使用

1. 配置分组、角色、权限
   * 一个分组包含一个或多个角色
   * 一个角色包含一个或多个权限
2. 将用户添加到分组
   * 用户会继承该分组内所有角色的权限
3. 也可以直接赋予用户某个角色
   * 不推荐，除非属于临时授予，一段时间之后撤回的情况。
4. 查询用户权限列表
5. 在自己的业务代码层，判断用户是否具备某种权限，从而执行对应逻辑。

假设获取到的用户权限列表如下：

```javascript
["email:login", "email:read", "email:write", "email:send"]
```

> 其中 ​email:login​ 完全为用户自定义，**我们推荐使用** ​**object:action**​ **的格式进行命名**。

  
开发者拿到用户权限列表之后，可以在业务代码层判断用户是否具备某一特定权限，如：

```javascript
if "email:login" not in user.permissionList:
    return "Permission Denied"
```

## 

